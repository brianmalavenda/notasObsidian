tipo::
tags:: #seguridad #red 
fecha:: 2026-08-25
links::
links_externos::

### Por qué el aislamiento de red no alcanza

- El aislamiento de internet corta el vector de ataque remoto original, pero no protege contra malware que ya viaja en el pendrive (autorun, macros infectadas, ejecutables camuflados, etc.).
- Cada alumno que conecta el pendrive a su PC personal (con estado de seguridad desconocido) y luego lo vuelve a conectar al nodo aislado, es un punto de entrada potencial.
- Un solo equipo comprometido en la cadena puede infectar el pendrive, que después infecta a los demás nodos "seguros".

### Cómo proteger los nodos de distribución

**1. Estaciones de "transferencia" dedicadas (kiosco/sacrificial)**  
En vez de que el pendrive toque directamente los 5 nodos aislados, que pase primero por una PC intermedia dedicada exclusivamente a escanear medios extraíbles, sin otra función. Si se infecta, se reinstala solo esa, sin tocar los nodos reales.

**2. Escaneo obligatorio con múltiples motores antivirus/antimalware**  
Antes de que cualquier pendrive toque un nodo aislado, pasar por esa estación de escaneo con al menos 2 motores distintos (ej: ClamAV + Windows Defender offline, o algo como VirusTotal en modo local si se puede).

**3. Deshabilitar Autorun/Autoplay en todos los equipos**  
Configuración de política de grupo (Windows) o udev rules (Linux) para que ningún archivo se ejecute automáticamente al insertar el USB.

**4. Whitelisting de aplicaciones (Application Control)**  
Herramientas como Windows AppLocker o Software Restriction Policies para que los nodos aislados solo puedan ejecutar binarios firmados/conocidos, bloqueando cualquier ejecutable no autorizado que venga en el pendrive. (USBGuard en Linux, Device Control/Endpoint Protector o políticas de grupo en Windows): solo se permite conectar dispositivos previamente registrados por su ID de hardware y número de serie. Cualquier pendrive nuevo o no autorizado queda bloqueado automáticamente, sin importar qué firmware tenga.

**5. Pendrives dedicados y de un solo sentido**
- Idealmente, usar pendrives exclusivos para "solo lectura" en los nodos aislados (existen pendrives con switch físico de write-protect).
- Evitar que el mismo pendrive se use para "subir" y "bajar" indistintamente en múltiples máquinas de alumnos.

**6. Topología estrella: Segmentación**  
El primer problema que resuelve esto es la trazabilidad. En vez de un único pendrive circulando por 30-50 personas, usar un esquema tipo "estrella": el docente entrega desde UN pendrive maestro (protegido, solo lectura) a pendrives individuales de cada alumno, evitando que los pendrives de alumnos se toquen entre sí ni con los nodos aislados directamente.

**7. Verificación de integridad (hashing)**  
Publicar el hash (SHA-256) de los archivos originales para que cada estación pueda verificar que el archivo no fue modificado/reemplazado en el camino.

**8. Monitoreo y logging local**  
Aunque estén aislados de internet, configurar logging local de eventos de inserción de USB y ejecución de procesos, para poder hacer forense si algo falla.

**9. Formateo y reinstalación periódica de los nodos**  
Si el volumen de tráfico de pendrives es alto, conviene tener un plan de reinstalación limpia periódica (por ejemplo, semanal) de esas 5 estaciones aisladas, más que confiar solo en la prevención.

**10. Restricción por tipo de archivo**: la estación solo debería aceptar extensiones esperadas (.pdf, .docx, .csv, datos de investigación específicos) y bloquear ejecutables, scripts, macros de Office, autorun.inf

**11. Bloqueo por clase de dispositivo**: el ataque BadUSB típico funciona porque un pendrive se hace pasar por teclado (HID) además de por unidad de almacenamiento, para "tipear" comandos maliciosos automáticamente. Se puede configurar el sistema para que rechace o alerte si un dispositivo conectado en el puerto de "solo almacenamiento" intenta enumerarse como teclado/adaptador de red — es la firma característica de este ataque.

---
#### Referencias

