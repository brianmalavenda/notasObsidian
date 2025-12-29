 Tags: #seguridad/ordenador/telemetria 
Links: [Seguridad]

## Telemetria Windows

**¿Qué es la telemetría?**
La telemetría en Windows 10 es un sistema que recopila información sobre el uso del sistema operativo, como el rendimiento del hardware, el uso de aplicaciones y la configuración del dispositivo. Estos datos se envían a Microsoft para ayudar a identificar problemas, mejorar el sistema y personalizar la experiencia del usuario. 

¿Cómo afecta la telemetría a tu PC?
- **Consumo de recursos:**
    La telemetría puede consumir recursos del sistema, como CPU y memoria, especialmente si se ejecuta en segundo plano. 
- **Privacidad:**
    Algunos usuarios se preocupan por la cantidad de datos que se recopilan y envían a Microsoft, incluso si son anónimos. 
- **Rendimiento:**
    En equipos con recursos limitados, la telemetría puede afectar el rendimiento general del sistema. 

**¿Cómo deshabilitar la telemetría?**
Existen varias formas de deshabilitar la telemetría en Windows 10, aunque algunas opciones pueden requerir herramientas de terceros o modificaciones en el registro del sistema. 

1. **A través de la configuración de Windows:**
    - Ve a "Configuración" > "Privacidad" > "Diagnóstico y comentarios". 
    - En "Datos de diagnóstico", selecciona "Básico" o "Opcional" para limitar la cantidad de datos enviados. 
    - Desactiva la opción "Enviar datos de diagnóstico opcionales". 
2. **A través del Editor del Registro:**
    - Abre el Editor del Registro (escribiendo "regedit" en la búsqueda). #seguridad/ordenador/registro
    - Navega a: `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection`. 
    - Crea un nuevo valor DWORD (32 bits) llamado "AllowTelemetry" y establece su valor en 0 para desactivar la telemetría. 
    
3. **A través del Programador de tareas:**
    - Abre el Programador de tareas (escribiendo "taskschd.msc" en la búsqueda). #seguridad/ordenador/programadortareas
    - Navega a: `Biblioteca del programador de tareas > Microsoft > Windows > Application Experience`. 
    - Desactiva las tareas relacionadas con la telemetría, como "Microsoft Compatibility Appraiser". 
4. **Desactivar Cortana**
5. **Servicios de Windows a desactivar**
	Estos #seguridad/ordenador/servicios están vinculados directamente con la recolección de datos: Connected User Experiences and Telemetry (DiagTrack) [Experiencia del usuario y telemetría asociadas] , dmwappushsvc (Push de datos de diagnóstico — muchas veces ya está deshabilitado) - [Diagnostic execution services], [Host del servicio de diagnóstico]
	* Abrí services.msc
	* Buscá cada uno
	* Doble clic > Tipo de inicio: Deshabilitado
	1. **Microsoft Office Click to Run**
		* Sirve para actualizar las herramientas Office, para eso revisa actualizaciones y compara. Como no usamos un office oficial esto no es útil para nosotros y por lo tanto lo podemos desactivar
	2. **Mostrar el servicio de directivas**
		* es un componente que permite detectar, solucionar y resolver problemas relacionados con el sistema
	3. **Optimizacion de distribucion**
		* es un servicio que ayuda a que las actualizaciones y aplicaciones de Microsoft, como las de la Tienda, se descarguen de forma más rápida y eficiente. Funciona compartiendo partes de estas descargas entre tu PC y otros dispositivos en tu red local o en internet. Sí, puedes deshabilitar este servicio, aunque hacerlo podría afectar la velocidad de descarga de actualizaciones y aplicaciones, especialmente si tienes una conexión a internet limitada. 
	4. **Servicio asistente para compatibilidad de programas**
		* ayuda a ejecutar programas antiguos que podrían no ser compatibles con la versión actual de Windows. Detecta problemas de compatibilidad conocidos y ofrece soluciones, como ejecutar el programa en modo de compatibilidad o buscar actualizaciones.
	5. **Servicio de geolocalización**
	6. **Servicio de Host HV**
		* Orientado al diagnóstico del rendimiento de maquinas virtuales que se ejecutan en Hyper-V.
	7. **Servicio de implementacion de AppX**
		* es un componente esencial de Windows que se encarga de la instalación, desinstalación y gestión de aplicaciones de la Tienda Windows (también conocidas como aplicaciones UWP).
	8. **Servicio de instalación de Microsoft Store**
		* es un componente de Windows que proporciona la infraestructura necesaria para el funcionamiento de la tienda de Microsoft y sus aplicaciones.
	9. **Servicio de repositorio de estado**
		* es un servicio de Windows que gestiona el almacenamiento de datos estructurados del usuario, como contactos, calendarios y mensajes. Permite que las aplicaciones de Windows Store y otras aplicaciones relacionadas accedan a esta información y también está involucrado en la captura de instantáneas de sesiones de navegación.
	10. **Servicio de uso compartido de datos**
		* se refiere a la funcionalidad que permite a los usuarios compartir archivos, carpetas e impresoras con otros dispositivos en una red. Puedes deshabilitarlo, pero esto afectará la capacidad de otros usuarios de acceder a tus recursos compartidos, como archivos o impresoras, a través de la red. 
	11. **Servicio de notificaciones de insersion de Windows_3fd69**
		* es una función de Windows que permite a los desarrolladores de aplicaciones enviar notificaciones a los usuarios, como actualizaciones de iconos, insignias y notificaciones sin procesar, desde sus propios servidores en la nube. En esencia, es un sistema que facilita la entrega de información relevante y actualizada a los usuarios de manera eficiente. 
	12. **Servicio de notificaciones de dispositivos conectados_3fd69**
	13. **Servicio de usuario de portapapeles_3fd69**
6. **Eliminar apps del sistema (bloatware con telemetría)**
	*   `Get-AppxPackage *xbox* | Remove-AppxPackage`
		`Get-AppxPackage *bing* | Remove-AppxPackage`
		`Get-AppxPackage *people* | Remove-AppxPackage`
		`Get-AppxPackage *solitaire* | Remove-AppxPackage`
7. **Crear reglas en Firewall Windows Defender para bloquear la salida de información de telemetría**
	* Ver este artículo para utilizar el #seguridad/ordenador/firewall para limitar la comunicación de telemetría hacia windows. [Crear reglas para Firewall](https://winaero.com/stop-windows-10-spying-on-you-using-just-windows-firewall/)
	* Cada línea de código esta en bash: netsh advfirewall firewall add rule name="telemetry_corpext.msitadfs.glbdns2.microsoft.com" dir=out action=block remoteip=131.107.113.238 enable=yes -> debe ser configurada para pasarse a Powershell y que cada línea quede así: New-NetFirewallRule -DisplayName "telemetry_vortex.data.microsoft.com" -Direction Outbound -Action Block -RemoteAddress 191.232.139.254 -Enabled True
	* Crear un ejecutable con este listado
	* Install-Module -Name ps2exe -Scope CurrentUser
	* Invoke-ps2exe .\bloquear_telemetria.ps1 -out .\bloqueo_telemetria.exe
	* Ejecutar como administrador