Tags: #seguridad/ordenador
Links: [Telemetria](Telemetria.md)

Conceptos: #bridge, #proxy
1.  Configurar OneDrive para desvincularlo de los equipos y borrar los archivos que quedaron en la nube asegurando de descargarlos primero en el PC local
2. Revisar la carpeta de Google Drive los archivos que tenemos con documentación "sensible" y borrarlos de la nube. 
3. Google Docs lo utilizamos para el EJES. Para poder cambiar eso tenemos que escribir en nuestras pc desde un word sin estar conectado con el Office 365, es decir, vinculado el Windows con un usuario. 

## Hardware
Chips en Intel y AMD
IME y PSP
## Sistema operativo
Instalar versiones de Windows LTSC o versiones de Linux para principiantes, con un parecido muy similar a Windows: LinuxMInt, PopoEs o escalar en aprendizaje y en seguridad instalando distribuciones como Tails, [Whonix](https://www.youtube.com/watch?v=BOkERgthqDw&t=2s).
Si continuamos con Windows por un tiempo para ir educando otros hábitos podemos eliminar la telemetría con ShupUp 10. Lo único que no se puede desactivar es las actualizaciones de Windows.
##### Opciones
1. **Whonix**
	Como requisito hay que tener instalado algún virtualizador de máquinas como virtualbox. Luego, descargando el "iso" que es un .ova de la página de https://www.whonix.org/. Desde la virtualbox vamos a importar el .ova descargado y allí creará dos máquinas virtuales. Una máquina virtual funciona como gateway, puerta de enlace a la conexión de internet de mi router. Y otra maquina virtual funciona como workstation o estación de trabajo donde con un solo adaptador de red se conecta a la maquina #seguridad/web/gateway y luego a internet. Es decir, mi workstation esta aislado de la conexión de internet. 

## Aplicaciones de rastreo

### Nube
1. **OneDrive**
	La nube de Windows que viene instalado de manera predeterminada en el S.O va a hacer una copia de seguridad de tus datos y sincronizar información que no queremos que sea pública sino que nunca salga de nuestro pc. Por lo tanto, al iniciar en windows, hacer solo una cuenta local y no una cuenta vinculada a un mail de Microsoft. Si no vinculamos una cuenta, OneDrive no tiene donde subir la data y asociarla al usuario. Despues lo desinstalamos.
2. **Antivirus**
	No instalar antivirus por fuera del Windows Defender que ya viene en el sistema operativo. Los antivirus, para detectar un programa malicioso tiene que estar constantemente registrando los "movimientos" del usuario y los programas que se ejecutan en la PC. A través del registro de "escaner de firma" detectan un archivo malicioso, pero también, al momento previo de instalarse en la maquina, el comportamiento de dichos programas son estandarizados y usados como la forma de detectar un posible virus. Son reglas del tipo, "si modifica tal fichero, de tal unidad de almacenamiento y despues modifica tal otro registro", entonces esto puede ser un virus, por el comportamiento puede inferir el tipo: **Troyano, Spyware, Malvare, Ramsware, etc**. Como tienen que hacer constantemente este registro de actividad, es similar a la telemetría que hace windows y en general hubo varios casos donde las empresas relacionadas a los antivirus fueron denunciadas por vender informacion de los usuarios a [Databrokers](./Databroker) que funcionan de intermediarios para vender informacion de comportamiento de usuarios para mercados específicos de publicidad dirigida. 
	***No es necesario que otro programa revise los datos de tu comportamiento si tu conducta de navegación es consciente de los problemas con los que nos podemos topar. Tiene más que ver con educación de navegación web.***



### Opciones
1.  **ClamWin**
	Un antivirus Open source que se ejecuta de manera manual. Podemos ver de programar un escaneo cada tanto tiempo sobre la carpeta del usuario. 
2. **Encriptar contenido del PC**
	Con programas como VeraCrypt podemos encriptar todos los datos desde el programa. Encriptadores de conocimiento cero (Proton)


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
