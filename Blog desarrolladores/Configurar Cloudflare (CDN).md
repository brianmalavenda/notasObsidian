
Cuando conectas un dominio que no es de testing, por lo menos con el hosting **bluehost** autoconecta al CDN de Cloudflare. Pero inicie una cuenta en cloudflare para poder administrar desde Cloudflare el trafico de la web. Desde ahi tenemos acceso a estadísticas basicas del sitios, Google Analitics y además podemos establecer algunas reglas de seguridad.

Una vez creado un usuario en Cloudflare, registrar su dominio con la opción "Busqueda rápida de registros DNS". Seleccionar el plan FREE y luego hay que agregar los registros DNS

### ¿Que son los registros DNS?
Los registros DNS (Domain Name System) son instrucciones almacenadas en el archivo de zona de su dominio. Traducen nombres de dominio legibles por personas (como `yourdomain.com`) en datos técnicos (como direcciones IP o ubicaciones de servidores de correo) que utilizan los navegadores y clientes de email .

#### Tipos de registro DNS comunes y lo que hacen

|**Escriba**|**Propósito**|**Ejemplo**|
|---|---|---|
|**A**|Dominio de puntos a una dirección IPv4|`@ → 192.0.2.1`|
|**AAAA**|Apunta a una dirección IPv6|`@ → 2001:db8::1`|
|**CNAME**|Crea un alias para otro dominio|`www → yourdomain.com`|
|**MX**|Dirige el email a los servidores de correo|`@ → mail.yourdomain.com`|
|**TXT**|Añade datos basados en texto (p. ej., SPF, DKIM)|`@ → v=spf1 incluir:_spf.google.com ~todos`|
|**SRV**|Define servicios como VoIP o chat|`_sip._tcp → URL de servicio y puerto`|

Comprobar propiedad del dominio y DNS
![[cloudflare_icann_lookup.png]]
Para eso se accede a https://lookup.icann.org/en/lookup y se busca el dominio el cual estamos dando de alta en Cloudflare. Si se encuentra al dominio y sus registros, proseguimos en cambiar los DNS por los de cloudflare
#### Registration data lookup tool

- Name: famosos50.com.ar
- Registry Domain ID: famosos50.com.ar
- Domain Status:
    
    active
    
- Nameservers:
    
    ns1.bluehost.com
    
    ns2.bluehost.com
    

Dates

- Registry Expiration: 2026-07-10 09:00:00 UTC
- Updated: 2025-07-15 13:11:51 UTC
- Created: 2025-07-10 07:34:01 UTC
- 
https://rdap.nic.ar/domain/famosos50.com.ar


![[cloudflare_DNS_reemplazar.png]]


Despues de verificar a través de Cloudflare que el dominio es propio. Hay que hacer la delegación de los DNS del dominio a los que te provee cloudflare asociados a la cuenta. Esto tarda unas horas. Hay que checkequear que esta ok.

## Configuración general

1. Ver en SSL configurar modo Full(Strict)
2. Revisar en menú “Analisis y registros”, seccion seguridad para ver que tipo de contenido es el que se solicita en las solicitudes, por ejemplo si el contenido más solicitado es en vez de HTTP el formato PNG de archivos quiere decir que o hay algo mal en el código o que hay un solicitud http externa que intencionalmente solicita archivos PNG como ataque.  
    Tambien revisar con que protocolo se esta visitando la página, es TLS 1.3?, es HTTP 1.1?
3. Quitar directamente los acceso con protocolo TLS 1.2  
4. Usar siempre HTTPS  

## Reglas de seguridad

#### Acceso solo Argentina
Despues de configurar la regla para solo aceptar solicitudes de Argentina, nos conectamos a una VPN y simulamos estar en otro país y verificamos que este andando OK la regla.

![[cloudflare_regla_seguridad.png]]
#### Redirects solicitudes de HTTP a HTTPS  
  
![[cloudflare_redirects_http.png]]

## Configuracion CDN

1. Configurar cache, reglas para imagenes
2. Configurar reglas contra bots y crawlers


Links:
Tags:
Links externos:
[Contenido de como configurar cloudflare](https://www.youtube.com/watch?v=I2mv4456l74)
[Reglas de cloudflare](https://github.com/search?q=cloudflare%20rules&type=repositories)
[Cloudflare](https://www.youtube.com/watch?v=G0P-fL39N-0)
