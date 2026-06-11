tipo::
tags::
fecha:: 2026-06-05
links::
links_externos::

El **Domain Name System (DNS)** es una infraestructura jerárquica y distribuida a nivel global que funciona como la "agenda telefónica" de Internet. Su propósito fundamental es la resolución de nombres, es decir, traducir identificadores alfanuméricos comprensibles para los humanos (como `ejemplo.com`) en direcciones IP binarias o de red (como `192.0.2.1` o `2001:db8::1`) que los enrutadores y nodos de transporte requieren para direccionar paquetes a través de la suite de protocolos TCP/IP. Sin este sistema, la capa de aplicación de la red sería impracticable para los usuarios, ya que requeriría memorizar los vectores numéricos de cada recurso en la red.

A nivel de arquitectura, el espacio de nombres de dominio se organiza en un árbol invertido con una estructura estrictamente jerárquica. En la cúspide se encuentra la **raíz (Root Zone)**, representada por un punto implícito al final de cualquier FQDN (Fully Qualified Domain Name). Inmediatamente abajo se ubican los **Top-Level Domains (TLD)**, divididos en genéricos (gTLD como `.com`, `.org`) y de código de país (ccTLD como `.ar`, `.cl`). El siguiente nivel corresponde a los dominios de segundo nivel (**Second-Level Domains** o SLD), que son las identidades registradas por entidades u organizaciones (por ejemplo, `uba` en `uba.ar`), seguidos por subdominios que subdividen administrativamente esa zona.

## Mecanismo de Funcionamiento y Flujo de Resolución

El proceso de resolución de una consulta DNS (Query) involucra una arquitectura cliente-servidor estructurada que alterna entre consultas recursivas e iterativas para minimizar la latencia y distribuir la carga de cómputo. El flujo se desencadena cuando una aplicación cliente (como un navegador web) solicita un recurso utilizando un FQDN.

```
[Cliente / Stub Resolver]
           │
           ▼ (Consulta Recursiva)
[Resolver Resolutor Local / ISP / Anycast] ────► [1. Root Nameservers (.)]
           │                                ◄──── (Devuelve referencia a TLD)
           ├───► [2. TLD Nameservers (.com)]
           │    ◄──── (Devuelve referencia a Servidor Autoritativo)
           └───► [3. Authoritative Nameservers (ejemplo.com)]
                ◄──── (Devuelve Registro A / IP final)
```

1. **Stub Resolver y Caché Local:** El sistema operativo del cliente intercepta la solicitud. Primero verifica su propio archivo `hosts` local y su memoria caché DNS. Si no encuentra una coincidencia válida (o si el TTL ha expirado), encapsula la petición en un datagrama UDP (habitualmente por el puerto 53) y la envía al **Resolutor DNS (Recursive Resolver)** configurado en la interfaz de red, que suele ser provisto por el ISP o servicios Anycast públicos (como `1.1.1.1` o `8.8.8.8`).
    
2. **Servidores Raíz (Root Server Nameservers):** El resolutor recursivo, actuando en nombre del cliente, inicia una búsqueda iterativa. Consulta a uno de los 13 servidores raíz lógicos del mundo para preguntar por la dirección de `ejemplo.com`. El servidor raíz no conoce la IP exacta, pero analiza el sufijo y responde con una referencia (redirección) hacia los servidores encargados del TLD correspondiente (`.com`).
    
3. **Servidores TLD (Top-Level Domain Server):** El resolutor toma esa referencia y repite la consulta al servidor de nombres del TLD `.com`. Este servidor analiza el SLD (`ejemplo`) y devuelve los registros NS (Name Server) que apuntan a los **Servidores de Nombres Autoritativos** delegados para administrar esa zona específica.
    
4. **Servidor Autoritativo (Authoritative Nameserver):** Es el último eslabón de la cadena. El resolutor interroga a este servidor autoritativo, el cual posee la base de datos maestra de la zona. Este responde con el registro de recurso solicitado (por ejemplo, el registro `A` con la dirección IPv4 final). El resolutor almacena esta respuesta en su caché local durante el tiempo especificado por el parámetro **TTL (Time to Live)** y finalmente entrega la dirección IP al cliente original para que este pueda iniciar el saludo de tres vías (3-way handshake) de TCP.
    

## Estructura de Registros y Ejemplos Prácticos Básicos

Para un administrador de sistemas o técnico de redes, la interacción directa con el DNS y la gestión de sus zonas se realiza a través de **Registros de Recursos (RR)** específicos dentro de los archivos de zona.

A continuación, se exponen los tipos de registros más críticos mediante una sintaxis estándar de archivo de zona (BIND):

Plaintext

```
; Tipo de Registro: A (IPv4 Address) y AAAA (IPv6 Address)
; Vinculan un FQDN directamente a una dirección de la capa de red.
www.ejemplo.com.      3600    IN    A       192.0.2.45
www.ejemplo.com.      3600    IN    AAAA    2001:db8:a::45

; Tipo de Registro: CNAME (Canonical Name)
; Crea un alias de un nombre a otro nombre canónico ya existente.
web.ejemplo.com.      86400   IN    CNAME   www.ejemplo.com.

; Tipo de Registro: MX (Mail Exchanger)
; Especifica los servidores de correo entrante para el dominio, con un orden de prioridad (menor número = mayor prioridad).
ejemplo.com.          14400   IN    MX      10 mail.ejemplo.com.
ejemplo.com.          14400   IN    MX      20 backup-mail.ejemplo.com.
```

### Diagnóstico en Terminal (Troubleshooting)

Como técnico, la validación del estado y propagación de estos registros se realiza mediante herramientas de línea de comandos como `dig` (Domain Information Groper) o `nslookup`. Un ejemplo práctico de consulta e interpretación de salida utilizando `dig` contra un servidor específico sería:

Bash

```
$ dig @8.8.8.8 www.ejemplo.com A +nocmd +nostats
```

**Salida típica del comando:**

Plaintext

```
;; got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 48219
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.ejemplo.com.		IN	A

;; ANSWER SECTION:
www.ejemplo.com.	3600	IN	A	192.0.2.45
```

El análisis técnico de esta salida confirma un estado `NOERROR` (resolución exitosa), con los flags `rd` (Recursion Desired) y `ra` (Recursion Available) activos, devolviendo en la sección de respuesta (`ANSWER SECTION`) el mapeo del registro `A` hacia la IP `192.0.2.45` con un TTL remanente de 3600 segundos para la caché del resolutor.

## Servidores de nombres

1. Cloudflare: 
	1. 1.1.1.1
	2. 1.0.0.1
2. Google: 
	1. 8.8.8.8
	2. 8.8.4.4
3. OpenDNS (cisco)
	1. 208.67.222.222
	2. 208.67.220.220
4. Quad9: 
	1. 9.9.9.9
	2. 149.112.112.112
5. Baidu: 
	1. 180.76.76.76
	2. 223.6.6.6
6. Alibaba:
	1. 223.5.5.5
	2. 223.6.6.6

---
#### Referencias

