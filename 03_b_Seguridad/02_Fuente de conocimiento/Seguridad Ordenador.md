tags: #seguridad/ordenador
links: [Telemetria](Telemetria.md), [Whonix](Whonix.md), [Databroker](Databroker.md), [[VPN]]

Conceptos: #bridge, #proxy
1.  Configurar OneDrive para desvincularlo de los equipos y borrar los archivos que quedaron en la nube asegurando de descargarlos primero en el PC local
2. Revisar la carpeta de Google Drive los archivos que tenemos con documentación "sensible" y borrarlos de la nube. 
3. Google Docs lo utilizamos para el EJES. Para poder cambiar eso tenemos que escribir en nuestras pc desde un Word sin estar conectado con el Office 365, es decir, vinculado el Windows con un usuario. 

## Hardware
Esto queda mas para investigación: Chips en Intel y AMD. IME y PSP
<<<<<<< HEAD:03_Seguridad/02_Fuente de conocimiento/Seguridad Ordenador.md
=======
## [Sistema operativo](SistemaOperativo.md)

Más de un 70% son usuarios de Windows y las herramientas cotidianas instaladas en ese S.O. Dentro de una de las características principales que hacen al rastreo del comportamiento del usuario se encuentra el constante registro de actividad, o [Telemetria](Telemetria.md), que se envía a Microsoft. Con estos datos de comportamiento se utilizan para armar un perfil digital destinado desde el control político hasta la publicidad dirigida.
##### Que hacer?
Instalar versiones de Windows LTSC o versiones de Linux para principiantes, con un parecido muy similar a Windows: LinuxMint, PopoEs o escalar en aprendizaje y en seguridad instalando distribuciones como Kali, Tails, [Whonix](Whonix.md).
Si continuamos con Windows por un tiempo para ir educando otros hábitos podemos eliminar la telemetría desinstalando programas pre-instalados en el sistema operativo y utilizando reglas que restrinjan la comunicación con servidores de Windows a través del Firewall. 
>>>>>>> cbef0a2542750bf1b3db34b7f269a33abdb21384:03_Seguridad/Blog seguridad/Ordenador.md

## Sistema Operativo
![[Sistema Operativo]]

## Antivirus
![[Antivirus]]

## Aplicaciones

### [Nube](Nube.md)
Las nubes son en definitiva equipos físicos que proveen de espacio de almacenamiento, una parte gratuitos porque ganan con los datos que se almacenan ahí para acceder a información de los usuarios y otras veces para ampliar el tamaño de almacenamiento se cobran, una "cuota de alquiler". 
### Login en aplicaciones web a través de email alias
adding o simplelogin

### Verificaciones de SMS de un solo uso
sms pull, sms plaza, o sms forzat

 
Nim connect?
### Mensajería
Treema, session, simplex, signal


### Proxy
shadowsocs, socs5
proxychange

OPSEC
### Firewall
Glasswire o Portmaster. Nos permitirá ver las conexiones de nuestro sistema. Tenemos que ver las conexiones de manera rutinaría.
### Utilizar una VPN

Proton VPN: Se ubica en Suiza, ofrece muchos tipos de protocolos como Wireguard o OpenVPN.
Mullvad VPN: Protocopolo de ofunzcacion UDP/TSP hace parecer una conexion normal. Servidores shadownhost. 
iVPN: Protocopolo de ofunzcacion UV2REI, cifrado post cuantico. Cuenta sin conexion con mail.

Crear uno propio

Servidores offshore: 
1984hosting en Islandia
Shala en Nieves

servidores DNS, que tiene que ver

### Gestor de contraseñas
bitwarden
2FA aaegis autenticator
### Utilizar un DNS
Quadd9, Cloudflare, Adguard, NextDNS
configuracion de NextDNS: https://www.youtube.com/watch?v=18Ihx_K0pVg

### Red Tor
Lokinet
**No es una violación sistémica:** La arquitectura de Tor **no incluye mecanismos para violación masiva**. Las vulnerabilidades son puntuales y mitigables:
- Usar Bridges + Obfs4 para ocultar uso de Tor
- Evitar plugins (Java, Flash)
- Activar "Security Slider" en Tor Browser

### Revision de estado de conexiones a los puertos de mi PC

	netstat -nao | findstr ESTABLISHED

si hay algun puerto raro abierto, podemos con netstat -a -b con alguna tubería de filtrado hacer referencia a ese puerto y chusmear mejor la conexión. Inclusive podemos cerrarla si tenemos dudas de la IP a la cual se esta conectando.
