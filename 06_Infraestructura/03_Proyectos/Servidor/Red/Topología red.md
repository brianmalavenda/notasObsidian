tipo:: #servidor/red 
tags:: #servidor/guia_instalacion 
fecha:: 2025-11-18
links::
links_externos::

CLUSTER DISTRIBUIDO CON ALMACENMAIENTO COMPARTIDO

![[red-2.drawio.png]]
## Router Bridge Huawei OptiXstar del ISP

Este router estará en modo bridge para poder "saltearlo" y administrar mi red desde mi router TP-Link
## Router TP-Link Omada Gigabit VPN Gateway

Router para administrar mis VLAN's que crearé en mi Siwtch keeplink. Este router tendrá conectado un punto de acceso para poder conectar clientes por fuera de la red de trabajo. 
Solo el tráfico **WAN saliente** de ciertas VLAN (ej. invitados, IoT) va por VPN. Los servidores productivos (SM_SERV700) deben salir por WAN directa (con firewall estricto).

## Switch Keeplink

Con el switch administraré mi Red de trabajo 

### Puertos
#### Puerto 1 al ER605
 
el puerto que conecta el **Router TP-Link** con el **Switch** esté en modo **TRUNK**. Esto permite que todas esas VLANs viajen por el mismo cable pero etiquetadas correctamente.

- Puerto 2 a SM_ADM →  VLAN admin
- Puerto 3 a SM_SAND →  VLAN sandbox
- Puerto 5 a SM_SERV700 → VLAN prod

>[!nota] Trunk
>asdfasdfasdfsad
>

### Redes virtuales VLAN

#### Empresarial

| **VLAN**    | **Nombre**         | **Función Clave**                                                            | **Nivel de Seguridad**                                                    |
| ----------- | ------------------ | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **VLAN 10** | **Administración** | PC Admin, Jump Server, Proxmox GUI, Switch UI, DNS                           | **Máximo**: Solo accesible vía VPN o desde tu PC física.                  |
| **VLAN 20** | **Producción**     | Nextcloud, WordPress, App Node.js.                                           | **Medio**: Expuesta a internet solo vía Nginx.                            |
| **VLAN 30** | **DMZ**            | **Nginx Proxy Manager**. Es la única que recibe tráfico directo de internet. | **Frontera**: Aislada del resto para que no "salten" de aquí a tus datos. |
| **VLAN 40** | **Dev / Sandbox**  | SM_SERV902. Pruebas de virtualización y código nuevo.                        | **Cuarentena**: Sin acceso a Producción ni Administración.                |
| **VLAN 60** | **Backups**        | **OpenMediaVault**. Almacenamiento centralizado de copias.                   | **Cofre**: Solo recibe datos, no inicia conexiones hacia afuera.          |

#### Residencial

| **VLAN**     | **Nombre**    | **Función Clave**                                 | **Configuración Especial**                                                            |
| ------------ | ------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **VLAN 100** | **Hogar**     | Tus celulares, notebooks de uso diario, Smart TV. | Acceso al DNS (AdGuard) y quizás al Nextcloud.                                        |
| **VLAN 200** | **Invitados** | Amigos o familiares que piden Wi-Fi.              | **Client Isolation** (no se ven entre ellos) y bloqueo total a todas las otras VLANs. |

#### VLAN YACANTO

**PC ADMIN**: Contiene una virtualización que es la que se conecta al Jumper y otra virtualización para el uso cotidiano.

**Jump Server:** Una VM dentro del servidor físico BLANCO que esta segurizada para solamente ser un puente entre la administracion y los servidores prod y sand.

**DNS**: lUn LXC/VM con **AdGuard Home** o **Pi-hole** para bloquear publicidad/malware y tener control. Esto hará el DNS interno será más seguro y rápido. **Todas** estas VLANs (desde Invitados hasta Producción) deben tener permiso en el firewall para hablar con la IP del AdGuard únicamente por el puerto **53**.

**TrueNAS**: maquina para hacer los backups de los servidores. Esto automatiza el guardado de información cada x tiempo depende del nodo que sea.

**Proxmox GUI (puerto 8006):** Es la interfaz web donde creas máquinas virtuales, ves el uso de CPU y gestionas discos. Si alguien entra aquí, tiene el control total de todos tus servidores (Producción, Sandbox, Backups). Será accesible solamente desde la VM en PC_ADMIN con acceso al jumper (definir nombre). Configurar para que **solo escuchen** a la VLAN 10. Si alguien está en la red de **Hogar (VLAN 100)** e intenta poner esa IP en el navegador, le dará un error de "página no encontrada". El servidor ni siquiera le responderá. Los switches gestionables (como tu Keeplink) tienen una opción llamada **Management VLAN**. Debes cambiarla de la "Default (1)" a la "10".
   
**Switch UI / Router UI:** Es el panel de configuración de tu **Keeplink** y tu **TP-Link Omada**. Aquí es donde defines qué puerto pertenece a qué VLAN. Si un atacante entra aquí, puede "borrar" las paredes que construimos y unir la red de Invitados con la de Administración en un segundo. Será accesible solamente desde la VM en PC_ADMIN con acceso al jumper (definir nombre). Configurar para que **solo escuchen** a la VLAN 10.

Si usas el rango `192.168.10.0/24`, podrías organizarlo así:

- **192.168.10.1:** Router TP-Link (Gateway).
- **192.168.10.2:** Switch Keeplink (Gestión).
- **192.168.10.10:** Proxmox Nodo 1 (SERV700).
- **192.168.10.11:** Proxmox Nodo 2 (SERV902).
- **192.168.10.50:** Jump Server (Tu puerta de entrada).
- **192.168.10.100:** Tu PC Admin (IP fija).


VLAN MGARCIA

Aquí debería vivir tu **Nginx Proxy Manager**. Si el proxy es comprometido, el atacante no tiene acceso directo a la base de datos o al Proxmox.

VLAN EZEIZA

Al tener la **VLAN 60**, si el día de mañana decides comprar un NAS físico (como un Synology o Terramaster) para sacar los backups del Serv700, solo tienes que conectarlo a un puerto del switch asignado a esa VLAN y listo, la arquitectura no cambia.

ASS

donde tengo un servidor con proxmox llamado SM_SERV700 donde tengo configuraré un DNS, nginx y nextcloud en un servidor virtual y otro servidor virtual con una app web de wordpress. (es necesario en este servidor virtual instalar adguard para el DNS y nginx?). Y por último tendre otro servidor virtual para una app propia desarrollada en node js. Mi otro servidor será un sandbox llamado SM_SERV902 que lo usaré para probar virtualizaciones antes de pasar al productivo SM_SERV700. Tambien aquí tengo proxmox instalado. Por último tengo una pc administradora llamada SM_ADM.



**Nginx**: Se necesita un único Nginx que haga proxy inverso para: Nextcloud, WordPress, tu app Node.js.

- En tu red, confías en el firewall del Router TP-Link para separar las VLANs. En esta topología, se aplica **Micro-segmentación**. Cada servidor tiene sus propias reglas de entrada y salida, independientemente de la red en la que esté.
    
- **Utilidad para ti:** En Proxmox, puedes activar el **Firewall nativo de Proxmox** a nivel de cada VM. Así, aunque alguien hackee una VM, el firewall de la VM de al lado bloquearía el movimiento lateral sin esperar a que el router actúe.


**Centralización de Datos (Backends):** Crea una VLAN exclusiva para bases de datos o almacenamiento de archivos (donde el Nginx no tenga permiso de entrada, solo tus apps).

### SM_SERV700

- VM1: **Nginx Proxy Manager** (expone puertos 80/443 al switch).    
- VM2: **Nextcloud** (sin nginx interno, usa el proxy).
- VM3: **WordPress**.
- VM4: **Tu app Node.js**.
- VM5 (o LXC): **AdGuard Home** (DNS para toda la red).

### SM_SERV902

en una VLAN separada (ej. VLAN 999) sin acceso a SM_SERV700 excepto puertos específicos (p.ej., solo rsync para copias de seguridad). Sin salida a internet si no necesita actualizaciones, o con restricción horaria.
### SM_ADM

VLAN de administración (acceso solo a switch, router, Proxmox).
* Acceso SSH con clave, no contraseña. 
* Acceso web a Proxmox solo desde esta IP.

## PC ADMIN

1. **Sistema Base (Host):** Instala una distribución estable como **Debian** o **Linux Mint**. Este será tu "suelo firme".
    
2. **VM de Navegación (Windows/Ubuntu):** Aquí es donde entras a YouTube, redes sociales y descargas cosas. Si un virus entra aquí, se queda "atrapado" en la VM. Solo tienes que borrar la VM y crear una nueva.
    
3. **VM de Administración (Ligera):** Una VM con una distro muy liviana (como **Xubuntu** o **Lubbock**) que tenga **solo** tus llaves SSH, tu navegador para el Proxmox GUI y tus herramientas de red.
    
    - **VPN:** Esta VM es la única que debería tener el túnel hacia tu **Jump Server**.
        

**¿Qué te recomiendo usar para las VMs?** Usa **VirtualBox** o **KVM/QEMU (Virt-Manager)**.

- **Virt-Manager** es más rápido porque es "pariente" de Proxmox (ambos usan KVM). Te servirá mucho para aprender cómo funciona tu servidor por dentro, ya que la lógica es casi idéntica.
## ALMACENAMIENTO

- Si el software de Proxmox se corrompe o borras por error la VM de OpenMediaVault, los datos siguen en el disco físico, pero "atrapados" en un formato virtual (como un archivo `.qcow2` o un volumen LVM). No podrías leerlos simplemente enchufando el disco a otra PC sin herramientas especiales.
    
- **La Recomendación Pro (Disk Passthrough):** En Proxmox, puedes "pasarle" el disco físico completo a la VM de OMV. De esta forma, OMV formatea el disco (por ejemplo en **EXT4** o **XFS**).
    
    - **La gran ventaja:** Si el `SERV700` explota mañana, sacas ese disco físico, lo conectas a cualquier otra computadora con Linux, y tus backups están ahí, legibles y listos. No dependes de que Proxmox esté vivo.

## Accespoint

Aqui se conectan regularmente 3 celulares y 2 notebooks. Estos clientes permanentes los pondré en una WLAN HOGAR y los invitados estaran en otra llamada WLAN INVITADOS.

- **WLAN HOGAR** (celulares, notebooks de confianza): VLAN 100.
- **WLAN INVITADOS**: VLAN 200 con **Client Isolation** + ancho de banda limitado + bloqueo a tus VLANs internas.

## Buenas prácticas de seguridad obligatorias

### En el router ER605
- Desactiva UPnP, WPS, ping desde WAN.
- Cambia puerto admin web (8080 → otro)    
- Firewall: reglas **por VLAN** (ej. bloquea VLAN 200 → VLAN 20).
- VPN: configura **WireGuard** (mejor que OpenVPN en este router).
### En el switch Keeplink
- Desactiva telnet, usa solo SSH.
- DHCP Snooping + ARP inspection en puertos de acceso.
- Spanning Tree (STP) para evitar bucles.

### En Proxmox (ambos servidores)
- Usa **firewall de host** (Proxmox tiene uno por nodo/VM).
- Actualizaciones automáticas de seguridad.
- Copias de seguridad nocturnas de VMs a otro disco o NAS.

### En AdGuard Home (DNS)
- Configura **DoT** (DNS over TLS) a Cloudflare/Quad9.
- Bloquea dominios de telemetría y malware.
- Asigna nombres locales (p.ej., `nextcloud.local` → IP de NPM).
    
### En Nginx Proxy Manager
- Certificados SSL automáticos (Let's Encrypt).
- Cabeceras de seguridad: HSTS, X-Frame-Options, etc.
- Autenticación básica para accesos administrativos.


## Checklist final para tu red segura

- [ ] ¿El ISP permite bridge real?
- [ ] ¿El AP soporta VLAN por SSID?
- [ ] ¿Separaste VLAN de administración (ningún cliente normal accede a puertos 8006/22 del servidor)?
- [ ] ¿Tienes reglas de firewall entre VLANs?
- [ ] ¿SM_SERV902 no puede alcanzar SM_SERV700?
- [ ] ¿La VPN del ER605 es para acceso remoto o para salida anónima? (No mezclar)
- [ ] ¿Tienes backup del switch/router/Proxmox?
- [ ] ¿Cambiaste todas las contraseñas por defecto?



#### REGLA DE PORT FORWARDING

NAT inverso: como tienes una regla de port forwarding (443 → IP interna de Nginx Proxy Manager), el router **cambia la IP destino** por la IP interna de tu NPM (ej. 192.168.20.10). 
Llega una solicitud con el paquete apuntando a mi ip local y acá antes que nada se hace el cambio a la ip de la VLAN donde tiene que ser, cambiando el puerto y la ip
```bash
# origen
Origen: 200.100.50.25 (cliente)
Destino: 181.55.10.20 (tu IP pública)
Puerto destino: 443 (HTTPS)

# nueva
Origen: 200.100.50.25
Destino: 192.168.20.10 (NPM)
Puerto destino: 443
```

Como el destino es 192.168.20.10 (VLAN 20 - Servidores), el switch envía el paquete solo al puerto donde está conectado SM_SERV700 (tu Proxmox). **El switch NO abre el paquete, solo lo conmuta por VLAN.**

El paquete llega a la interfaz de red del servidor. Proxmox lo entrega a la VM que tiene la IP 192.168.20.10.

[Proxmox Host - SM_SERV700]
• Interfaz de red bridge (vmbr0) con VLAN filtering
• Recibe paquete con VLAN 20
• Kernel de Proxmox enruta a la VM correcta:
    DEST MAC: de la VM Nginx Proxy Manager
       │
       ▼
[VM: Nginx Proxy Manager - 192.168.20.10]
• Nginx recibe conexión en :443
• Termina SSL/TLS (descifra con certificado Let's Encrypt)
• Lee Host header: "tudominio.com"
• Busca regla de proxy_pass:
    location / { proxy_pass http://192.168.20.20:80; }

**Nginx Proxy Manager (NPM) recibe la petición HTTPS.**
1. **Termina SSL** (tiene certificado para `midominio.com`).
2. **Lee el Host Header** (¿a qué dominio/subdominio va?).
3. **Reenvía internamente** (sin SSL) a la VM de WordPress.

**Reenvío interno:**

```bash
# desde mi red local
Origen: 200.100.50.25
Destino: 192.168.20.10 (NPM)
Puerto destino: 443

# en mi sm_serv700
Origen: 192.168.20.10 (NPM)
Destino: 192.168.20.30 (VM WordPress)
Puerto: 80 (HTTP)

```

Este reenvío ocurre **dentro del mismo SM_SERV700** (o a otra VM en la misma VLAN 20). No sale del switch.

[Nginx Proxy Manager 192.168.20.10]
       │
       ▼
Nueva conexión HTTP (interna, sin SSL):
GET / HTTP/1.1
Host: tudominio.com
X-Forwarded-For: 186.45.67.89  ← Cliente original
X-Forwarded-Proto: https
       │
       ▼
[Switch Keeplink - VLAN 20]
• Tráfico interno entre VMs: mismo broadcast domain
• Baja latencia, sin pasar por ER605

No sale del servidor? no vuelve al switch? no, se ejecuta lo siguiente:
1. Switch físico Keeplink envía paquete a SM_SERV700 por el cable. 
2. Proxmox recibe el paquete en `enp2s0` → lo pasa al bridge `vmbr0`.
3. El bridge `vmbr0` entrega el paquete directamente a la VM de Nginx (por su interfaz virtual).
4. Nginx procesa y decide: "esto va a WordPress en la misma VLAN".
5. Nginx genera un **nuevo paquete** (con origen=Nginx, destino=WordPress).
6. Ese nuevo paquete **sale de la VM Nginx** hacia el bridge `vmbr0`.
7. El bridge `vmbr0` **lo entrega directamente a la VM WordPress**.
8. **Nunca toca el cable físico** ni el switch Keeplink.
       │
       ▼
[VM: WordPress - 192.168.20.20:80]
• Apache/Nginx interno recibe petición
• WordPress procesa: carga plugins, consulta MySQL, genera HTML
       │
       ▼
Respuesta HTTP 200 OK con contenido HTML

```bash
Switch físico → cable → enp2s0 → vmbr0 → Nginx VM 
                                              ↓ (nuevo paquete)
                                         vmbr0 → WordPress VM
```

**¿Dónde interviene AdGuard Home aquí? ****En ningún lado**, porque el cliente externo no usa tu DNS interno.  AdGuard solo sirve para:
- Tus dispositivos internos (celular, notebook, SM_ADM) resuelvan nombres locales (p.ej., `nextcloud.local` → IP).
- Bloquear publicidad/malware para toda tu red local.

**Si WordPress necesita llamar a una API interna** (ej. a Nextcloud), entonces sí:  
WordPress consultará a AdGuard (si configuraste DNS de la VM hacia AdGuard) y AdGuard responderá con la IP interna.

## Resumen del flujo (modo tabla)

|Paso|Dispositivo|Acción clave|¿VPN aplica?|
|---|---|---|---|
|1|ISP Router (bridge)|Pasa paquete|No|
|2|ER605|Port forwarding 443 → NPM|No (regla previa)|
|3|Switch Keeplink|Conmuta por VLAN 20|No|
|4|SM_SERV700 (NPM VM)|Termina SSL, reenvía HTTP a WordPress|No|
|5|WordPress VM|Genera respuesta|No|
|6|Mismo camino inverso|-|No|

## Advertencia de seguridad crítica para tu diseño

**Problema**: Tienes un solo servidor físico (SM_SERV700) con todas las VMs. Si alguien compromete WordPress (ej. plugin vulnerable), podría escalar a otras VMs (Nextcloud, Node, NPM, AdGuard).

**Solución obligatoria en tu switch Keeplink**:

- Regla firewall: La VM WordPress **solo** puede hablar con NPM (puerto 80) y con la base de datos.
    
- **No** puede hablar con Nextcloud ni con AdGuard ni con la VM Node.
    
- Esto se logra con **ACLs (Access Control Lists)** en el switch a nivel de puerto o con VLAN privada (PVLAN).

CORRÍJEME TODO LO QUE PUEDAS PARA TENER MI TOPOLOGÍA DE RED BIEN DEFINIDA Y SEGURIZADA. TEN EN CUENTA QUE ES UNA CONFIGURACION HOGAREÑA QUE APUNTA A DESARROLLAR LAS POLÍTICAS DE MAXIMA SEGURIDAD Y BUENAS PRACTICAS PORQUE SERÁ PRONTAMENTE AMPLIADO A UNA EMPRESA PEQUEÑA


### Fase 1 (Ahora - Hogar seguro)

- Implementar VLANs + reglas firewall básicas
- Hardening de Proxmox y VMs
- VPN WireGuard para acceso remoto seguro
- Backup 3-2-1: 3 copias, 2 medios, 1 fuera del sitio

### Fase 2 (Pre-empresa: 3-6 meses)

- Migrar ER605 a modo firewall dedicado (desactivar funciones router si usas otro)
- Implementar monitoreo: Zabbix ligero o Netdata en VM dedicada
- Documentar topología, credenciales y procedimientos de recuperación
- Pruebas de penetración básicas: `nmap` desde VLAN 50 para verificar aislamiento

### Fase 3 (Empresa pequeña: 6-12 meses)

- Evaluar firewall empresarial (pfSense/OPNsense en VM o hardware dedicado)
- Implementar autenticación centralizada (FreeRADIUS o LDAP ligero)
- Políticas de actualización automática con ventana de mantenimiento
- Plan de respuesta a incidentes documentado


## Escenarios de Ataque

Imagina que tu **WordPress** en la VLAN de Producción es hackeado a través de un plugin vulnerable.

1. El atacante ahora tiene control de esa VM.
    
2. Lo primero que hará será escanear la red buscando IPs.
    
3. Intentará entrar a `192.168.1.1` (Router) o buscará el puerto `8006` (Proxmox).
    
4. **Como tú moviste esas interfaces a la VLAN 10**, el atacante no verá nada. Para él, el resto de la red es "desierto".


### Análisis Detallado de Tus Escenarios

#### Escenario 1: Comunicación Interna (NAS -> Cliente)

- **Tu análisis:** La comunicación entre tu NAS (10 Gb) y tu PC cliente (2.5 Gb) está limitada a 2.5 Gb.
- **¿Es correcto?** **Sí.** La velocidad de transferencia en una conexión punto a punto está determinada por la interfaz más lenta de los dos dispositivos que se comunican. En este caso, tu PC no puede recibir datos a más de 2.5 Gb/s, incluso si el NAS puede enviarlos a 10 Gb/s.
    
- **¿Sigue siendo una gran mejora?** **¡Absolutamente!** Compara esto con un escenario común:
    
    - **Red Gigabit tradicional:** Límite de ~113 MB/s.
        
    - **Con tu nuevo setup (2.5 Gb):** Límite de ~280 MB/s.  
        Es una mejora de **casi 2.5 veces** en velocidad. Para transferir archivos grandes (vídeos, backups, librerías de fotos) la diferencia es abismal.
        

#### Escenario 2: Comunicación con Internet (ISP -> Cliente)

- **Tu análisis:** Si tuvieras un ISP de 10 Gb, tu PC cliente (2.5 Gb) solo podría descargar a un máximo de 2.5 Gb.
    
- **¿Es correcto?** **Sí, pero con un matiz importante.** Es casi imposible que una sola aplicación o descarga sature un enlace de 2.5 Gb desde internet. Los servidores, la ruta de internet y otros factores suelen ser el cuello de botella primero. Sin embargo, en teoría, el límite físico de tu PC es 2.5 Gb.
    
- **La ventaja real aquí es la capacidad multi-dispositivo.** Mientras tu PC se descarga un juego a 2.5 Gb, otro miembro de la familia podría estar viendo 4K en streaming, otro en videollamada, y tu NAS podría estar haciendo una copia de seguridad en la nube, y el switch manejaría todo este tráfico simultáneamente sin congestionarse, gracias a su alto ancho de banda de backplane.
    

---

### ¿Por qué SÍ Vale la Pena ese Switch? (La Visión de Futuro y Escalabilidad)

Comprar un switch no es solo para lo que tienes HOY, sino para lo que tendrás en los próximos años. Aquí está el valor real de tu elección:

1. **Inversión de Futuro para tu NAS:** Ya tienes tu NAS con 10 Gb. Es un activo valioso. Si en el futuro compras otra PC o una estación de trabajo con una tarjeta de red de 10 Gb, podrás conectarla directamente al puerto SFP (usando un transceptor RJ45 10GBase-T si es necesario) y **¡alcanzarás la velocidad completa de 10 Gb entre ellos!** (~1.1 GB/s). El switch no será un obstáculo.
    
2. **Múltiples Clientes Rápidos Simultáneos:** Imagina que tienes 2 o 3 PCs con puertos de 2.5 Gb. Todas ellas podrán transferir datos con el NAS a 2.5 Gb **al mismo tiempo**. El switch de 10 Gb al NAS puede manejar el tráfico agregado de varias PCs de 2.5 Gb sin problemas. Un switch con uplink de solo 1 Gb se congestionaría en este escenario.
    
3. **La Arquitectura Correcta:** Has identificado la arquitectura ideal: **"Varios puertos rápidos de acceso + un enlace de agregación de alta velocidad"**. Esto es exactamente para lo que están diseñados switches como el que describes (por ejemplo, los QNAP QSW-2104-2T o similares). Los 2.5 Gb son para los dispositivos clientes y el 10 Gb es el "camión de carga" que lleva todo el tráfico a un punto central como el NAS.
    

### Conclusión y Recomendación

**No compres un switch con todos los puertos de 2.5 Gb.** Sería un error porque:

- **Estrangularías tu NAS:** Conectar tu NAS de 10 Gb a un puerto de 2.5 Gb sería un desperdicio total de su potencial, limitándolo artificialmente a la misma velocidad que tus PCs.
    
- **Perderías escalabilidad:** No podrías añadir un dispositivo de 10 Gb en el futuro sin cambiar todo el switch.
    

**Tu elección inicial es la correcta:** Un switch con **4 puertos RJ45 de 2.5 Gb + 1 puerto SFP+ de 10 Gb** es el punto óptimo en cuanto a precio-rendimiento-futuro para tu escenario.

**Resumen final:**

- **Tu análisis técnico es impecable:** Sí, estás limitado por el puerto más lento en cada conexión directa.
    
- **Pero la conclusión práctica es comprarlo:** La mejora de 1 Gb a 2.5 Gb para tus clientes es enorme, y el puerto de 10 Gb protege tu inversión en el NAS y te da un camino de actualización para el futuro.


---
#### Referencias

