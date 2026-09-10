tipo::
tags::
fecha:: 2026-08-28
links::
links_externos::

Crear un pendrive booteable con Windows

## Eliminación de telemetría y bloatware

Abrir PowerShell como Administrador y ejecuta el módulo de _Chris Titus Tech_ 

```powershell
iwr -useb https://christitus.com/win | iex
```

Esto abrirá una interfaz gráfica. Ve a la pestaña "Tweaks" y selecciona todo lo que vamos a borrar de la instalación base de Windows 11.

1. Bit locker - Disable
2. Microsoft store Recommended Search Result - Disable
3. Telemetry - Disable
4. Temporary Files - Remove
5. Widgets - Remove
6. Microsoft Edge - Debloat
7. Microsoft Edge - Remove
8. Microsoft Onedrive - Remove
9. Windows AI - Disable And Remove

Al iniciarse el proceso de borrado, abrirá una terminar con el nombre de WinUtil y mostrará todo el proceso de desinstalación. Preguntará si continuar con la eliminación de Widgets a lo que ponemos "S" para corroborar que si.

Continuaremos con otros scripts que "afinará" el borrado de otras aplicaciones secundarias de Windows que no necesitamos y por lo tanto sacaremos

Ir a Configuración -> Aplicaciones -> Aplicaciones instaladas
Allí eliminaremos:
1) Asistente rapido de Windows
2) Notas rápidas
3) Acceso a equipo remoto
4) El tiempo
5) Family
6) Microsoft Clipchamp
7) Microsoft Noticias
8) Microsoft Teams
9) Microsoft To Do
10) Power Automate
11) Solitarie & Casual Games
12) Xbox
13) Xbox Live

## Instalar aplicaciones

Desde esta misma herramienta se puede instalar a través de Winget aplicaciones como:

* Brave: navegador para uso más privado
* Google Chrome: navegador para uso personal
* Libre Office: Word y Excel en código abierto 
* Obsidian: tomar notas relacionadas entre ellas y armar una base de conocimiento
* WinRAR: para comprimir y descomprimir carpetas de archivos

## Desactivar SysMain (Superfetch)

Este servicio pre-carga aplicaciones en la RAM pensando en discos mecánicos (HDD). Tienes un SSD, por lo que este servicio solo genera estrés innecesario en el disco y la CPU.

```powershell
Stop-Service "SysMain" -Force
Set-Service "SysMain" -StartupType Disabled
```

### Desactivar el Indexador de Búsqueda

Indexa cada archivo para que la búsqueda de Windows sea rápida. En un equipo de investigación, los estudiantes usarán el explorador de archivos directamente. Apagar esto libera mucha CPU en segundo plano.

~~~powershell
Stop-Service "WSearch" -Force
Set-Service "WSearch" -StartupType Disabled
~~~
## Activar Windows

```powershell
Invoke-RestMethod https://get.activated.win | Invoke-Expression
```

Opción 1, 


## Herramientas de protección
#### Escáner de USBs Automatizado (La solución a tu problema principal)

Para los USBs, el mayor riesgo es el "AutoRun" (ejecución automática). Lo bloquearemos y crearemos un script que escanee cualquier USB al ser conectado usando el motor de Defender.

**Paso A: Desactivar AutoRun en Windows (Crítico)** Abre PowerShell como Administrador y ejecuta esto para bloquear la ejecución automática de USBs mediante el registro:

~~~powershell
# Desactivar AutoRun en todas las unidades
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "NoDriveTypeAutoRun" -Value 255

Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "NoAutoRun" -Value 1
~~~

**Paso B: Crear el script de escaneo automático de USB**

1. Abre el Bloc de notas y pega este código de PowerShell:

```powershell

# Script: EscaneoAutomaticoUSB.ps1

$query = "SELECT * FROM __InstanceCreationEvent WITHIN 2 WHERE TargetInstance ISA 'Win32_LogicalDisk' AND TargetInstance.DriveType = 2"

Register-WmiEvent -Query $query -Action {

$driveLetter = $Event.SourceEventArgs.NewEvent.TargetInstance.DeviceID

Add-Type -AssemblyName System.Windows.Forms

[System.Windows.Forms.MessageBox]::Show("Dispositivo USB detectado ($driveLetter). Iniciando escaneo de seguridad...", "Escaneo de Seguridad", 0, 64)

Start-MpScan -ScanType CustomScan -ScanPath $driveLetter

[System.Windows.Forms.MessageBox]::Show("Escaneo de $driveLetter completado. Es seguro usar el dispositivo.", "Escaneo Finalizado", 0, 64)

}
```

2. Guárdalo en `C:\Scripts\EscaneoAutomaticoUSB.ps1`.
3. Configura este script para que corra en segundo plano al iniciar sesión. Puedes usar el **Programador de tareas** de Windows para que ejecute este script (`powershell.exe -WindowStyle Hidden -File C:\Scripts\EscaneoAutomaticoUSB.ps1`) cada vez que cualquier usuario inicie sesión.

#### 4. Herramientas de Segunda Opinión (Para escaneos manuales)

Los estudiantes deben poder escanear descargas sospechosas. Instalaremos estas herramientas (no open-source, pero gratuitas y estándar en la industria):

1. **Malwarebytes Free:** Es el rey para la detección de PUPs (Programas Potencialmente No Deseados) y spyware. No interfiere con Windows Defender. Instálalo pero asegúrate de desactivar la opción de "Iniciar con Windows" o la "Versión de prueba Premium" para que solo se use bajo demanda.
2. **AdwCleaner (de Malwarebytes):** Es un ejecutable portátil. Descárgalo y déjalo en el Escritorio. Es brutal para limpiar barras de herramientas y adware que los estudiantes instalen sin querer.
3. **VirusTotal Uploader:** Descarga la aplicación de escritorio de VirusTotal. Permite a los estudiantes hacer clic derecho sobre cualquier archivo sospechoso descargado de internet y enviarlo a analizar por más de 70 antivirus en la nube antes de ejecutarlo.

#### 5. Análisis Forense Ligero (Opcional pero recomendado)

Como eres el encargado forense, te recomiendo instalar **Sysmon (System Monitor de Sysinternals)**. No detecta malware por sí mismo, pero registra cada proceso que se ejecuta, conexiones de red y creación de archivos.

- Si un equipo es comprometido, los logs de Sysmon te dirán exactamente qué pasó.
- Puedes instalarlo ejecutando como administrador: `sysmon -accepteula -i` (necesitas descargarlo de la web de Microsoft Sysinternals).

---
#### Referencias

