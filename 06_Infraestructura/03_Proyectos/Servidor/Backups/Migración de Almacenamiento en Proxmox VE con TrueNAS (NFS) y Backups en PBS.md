tipo::
tags:: #backups 
fecha:: 2026-06-04
links:: [[Proxmox_PVE]]
links_externos::

Pasamos de una configuración de red rígida e ineficiente a un modelo unificado, performante y, sobre todo, blindado ante catástrofes utilizando [[Proxmox_PVE]] (PVE), **TrueNAS (NFS)** y **Proxmox Backup Server (PBS)**.

A continuación, les detallo el proceso paso a paso, los dolores de cabeza (errores) con los que nos topamos, cómo los solucionamos y el resultado final.

## 1. El Escenario Inicial y el Objetivo

Teníamos una máquina virtual de producción (`vm-apps`) corriendo [[Nextcloud]] mediante un stack de Docker. El problema era que el volumen de datos apuntaba de manera rígida a un montaje de red antiguo.

**El objetivo:** Mudar ese almacenamiento a un pool de discos mecánicos en **RAID (Mirror)** gestionado por **TrueNAS** a través del protocolo **NFS**, pero haciéndolo de la "forma correcta": asignando un disco virtual controlado por el hipervisor (PVE) para que **Proxmox Backup Server (PBS)** pueda realizar copias de seguridad incrementales a nivel de bloques y deduplicadas de manera nativa.

## 2. Paso a Paso de la Migración

### Paso 2.1: El "Abrazo" entre Proxmox y TrueNAS

El primer paso fue montar el dataset de TrueNAS (dedicado a almacenamiento de VMs) dentro de nuestro Datacenter de Proxmox como un recurso NFS compartido. Lo llamamos `hdd-cloud-stg`.

### Paso 2.2: Creación del Disco Virtual (El primer gran problema)

Fuimos al Hardware de la VM de Nextcloud e intentamos añadir un **Hard Disk** secundario en el bus SCSI utilizando el storage NFS recién creado, apuntando a un tamaño masivo (3 TB) con formato `qcow2` (ideal para snapshots y compatibilidad con PBS).

#### 🛑 El Problema (Task Error / Timeout)

Al darle "Add", el proceso se congeló y la interfaz web nos arrojó el siguiente error:

Plaintext

```
TASK ERROR: unable to create image: 'storage-hdd-cloud-stg'-locked command timed out - aborting
```

**¿Por qué pasó esto?** Por defecto, al crear discos virtuales sobre almacenamiento en red (NFS), Proxmox fuerza el parámetro `preallocation=metadata`. Intentar escribir de un solo tirón la estructura de metadatos de un bloque tan grande (3 TB o incluso 500 GB) sobre discos rígidos mecánicos a través de la red satura el tiempo de respuesta del hilo de Proxmox, provocando que el comando exceda el límite de tiempo (_Timeout_) y bloquee la VM con un _lock_.

#### 🛠️ La Solución

1. **Destrabar la VM por consola:** Entramos a la Shell del nodo Proxmox y forzamos la remoción del candado:
    
    Bash
    
    ```
    qm unlock 102
    ```
    
2. **Matar procesos zombis:** Limpiamos cualquier proceso `qemu-img` que haya quedado colgado en el fondo.
    
3. **Desactivar la Preasignación:** Fuimos a _Datacenter -> Storage -> Edit hdd-cloud-stg_ y cambiamos la opción **Preallocation** de _Default/Metadata_ a **`Off`** (None). Al hacer esto, el archivo `.qcow2` se crea de forma _Thin_ (delgada) ocupando apenas unos kilobytes inicialmente y creciendo de forma dinámica.
    
4. **Creación escalonada:** Creamos el disco con un tamaño inicial menor (ej. 500 GB) que se generó instantáneamente en menos de dos segundos. _(Dato de color: Proxmox permite expandir este tamaño en caliente con "Disk Action -> Resize" más adelante sin sufrir jamás un timeout)._
    
5. **Optimización de rendimiento:** Configuramos la caché del disco en **`Write back`** e incluimos el parámetro **`IO thread`** activo para delegar un hilo exclusivo de procesamiento a las escrituras pesadas de Nextcloud.
    

### Paso 2.3: Formateo y Montaje Persistente dentro de la VM

Una vez asignado el hardware por el hipervisor, entramos a la terminal de la máquina virtual (Debian) para inicializar el disco:

1. **Formateo en Ext4:**
    
    Bash
    
    ```
    mkfs.ext4 /dev/sdb
    ```
    
2. **Montaje permanente por UUID:** Para evitar que las letras de los discos cambien al reiniciar, extrajimos el UUID del disco y lo añadimos al archivo `/etc/fstab` con opciones de optimización para discos mecánicos (mitigando escrituras innecesarias de acceso):
    
    Plaintext
    
    ```
    UUID=tu-uuid-aqui /mnt/cloud-data ext4 defaults,noatime,nodiratime 0 2
    ```
    
3. **Mudanza con Rsync:** Sincronizamos la data histórica del viejo montaje a la nueva carpeta montada manteniendo intactos los atributos de los archivos:
    
    Bash
    
    ```
    rsync -avzP /ruta-vieja/ /mnt/cloud-data/
    ```
    

### Paso 2.4: Mapeo en Docker y el Conflicto de Permisos

Con los datos en su nuevo lugar físico, fuimos al archivo `docker-compose.yml` (o el stack de Portainer) de Nextcloud y remapeamos el volumen de datos:

YAML

```
volumes:
  - /mnt/cloud-data:/var/www/html/data
```

#### 🛑 El Segundo Problema: Error Interno del Servidor e Invalidez

Al levantar el stack, el navegador nos devolvió una pantalla azul de Nextcloud indicando un _"Error interno del servidor"_, que luego mutó a:

Plaintext

```
Error: Su carpeta de datos es inválida. Asegúrese que exista un archivo llamado ".ocdata" en la raíz...
```

**¿Por qué pasó esto?** Nextcloud utiliza un archivo oculto de seguridad llamado `.ocdata` en la raíz de los datos para verificar que el disco no se haya descolgado. Al hacer el `rsync`, los archivos ocultos (que empiezan con punto) suelen quedarse atrás. Además, las versiones modernas exigen que este archivo contenga una firma específica. Por último, Docker corre internamente Nextcloud con el UID `33` (`www-data`), y al mudar la carpeta, los permisos quedaron asignados al usuario root de la VM.

#### 🛠️ La Solución

1. **Inyectar la firma en el archivo oculto:**
    
    Bash
    
    ```
    echo "# Nextcloud data directory" > /mnt/cloud-data/.ocdata
    ```
    
2. **Asignar la propiedad absoluta a Docker (UID 33):**
    
    Bash
    
    ```
    chown -R 33:33 /mnt/cloud-data
    chmod -R 750 /mnt/cloud-data
    ```
    

Reiciamos el contenedor (`docker compose restart`) y ¡vualá! Nextcloud levantó al instante, impecable y reconociendo todo el historial de los usuarios.

## 3. Integración con Proxmox Backup Server (PBS)

Con la arquitectura de almacenamiento corregida, llegó el momento de integrarla con nuestra netbook destinada a **Proxmox Backup Server**.

### El desafío del Datastore en un único disco

Al intentar configurar el Datastore desde la interfaz web del PBS en la netbook, nos topamos con que indicaba _"No Disks unused"_. Esto se debe a que la netbook cuenta con un único almacenamiento físico (`/dev/sda`) donde ya está instalado el sistema operativo del PBS. Dado que la interfaz web sólo permite formatear discos secundarios vacíos para proteger al usuario de borrados accidentales, tuvimos que inicializar el espacio por la Shell.

#### 🛠️ Solución por Consola en el PBS:

1. Creamos la carpeta física dentro del almacenamiento del sistema:
    
    Bash
    
    ```
    mkdir -p /mnt/local-backups
    chown -R backup:backup /mnt/local-backups
    ```
    
2. Le ordenamos al gestor del PBS que cree el Datastore apuntando a esa carpeta:
    
    Bash
    
    ```
    proxmox-backup-manager datastore create local-ds /mnt/local-backups
    ```
    

Al instante, el almacén `local-ds` apareció operativo en la interfaz web.

### Enlace y Prueba de Fuego

Fuimos a nuestro Proxmox VE principal, añadimos el almacenamiento de tipo _Proxmox Backup Server_ bajo el ID de nuestra preferencia, completando la IP, el usuario `root@pam`, el Datastore `local-ds` y la huella digital (_Fingerprint_) obtenida del PBS.

**La Prueba de Fuego:** Fuimos a la VM de Nextcloud, entramos a la pestaña **Backup**, seleccionamos nuestro nuevo PBS y le dimos a **Backup Now** en modo **Snapshot** (en caliente, sin detener el servicio).

El sistema completó la tarea exitosamente. Lo más impresionante fue la segunda prueba: al lanzar un backup inmediatamente después del primero, este demoró apenas **escasos segundos**. Gracias al diseño del PBS, solo se transmitieron los micro-bloques modificados, demostrando la eficiencia brutal de la deduplicación.

## 4. Conclusión y Lecciones Aprendidas

Mudar almacenamiento en entornos de producción hogareños o profesionales requiere entender cómo dialogan el hipervisor y el almacenamiento en red. Apagar la preasignación de metadatos en entornos NFS de discos mecánicos es vital para evitar bloqueos del sistema. Asimismo, estructurar los datos dentro de discos virtuales (`.qcow2`) en lugar de montajes directos por red dentro de las VMs es la clave absoluta para aprovechar las bondades de herramientas corporativas como Proxmox Backup Server.


# Migración de Volumen de Datos en Nextcloud (Docker) con la Imagen de LinuxServer.io

Al realizar una migración de almacenamiento de un entorno Nextcloud corriendo bajo Docker, es común encontrarse con dolores de cabeza si no se tienen en cuenta las particularidades de la imagen utilizada. 

En esta nota, documentamos los errores encontrados y la solución paso a paso para mover con éxito el directorio `/data` a una nueva unidad de almacenamiento en un stack de Docker que utiliza la popular imagen de **LinuxServer.io**.

---

## 🛑 Errores Encontrados durante la Migración

### Error 1: Archivo de validación oculto incorrecto (`.ncdata`)
Se creó por error el archivo `.ocdata` (herencia de la nomenclatura de ownCloud) en lugar de **`.ncdata`** (específico de las versiones actuales). Además, contenía dos líneas de texto en vez de una sola, lo que Nextcloud detecta como una firma inválida.

### Error 2: Conflicto de UID/GID (Permisos de ejecución)
A diferencia de la imagen oficial de Nextcloud que corre bajo el usuario `www-data` (UID 33), **la imagen de linuxserver.io utiliza internamente el UID 1001 (usuario `abc`)**. Al dejar los archivos con el propietario de la máquina virtual o del mapeo clásico, el contenedor es incapaz de escribir en el directorio.

### Error 3: Sincronización con `rsync` incompleta
El comando de `rsync` inicial omitió carpetas críticas de los usuarios y no migró el contenido completo de la caché y configuraciones alojadas en `appdata`.

---

## 🛠️ Solución Paso a Paso

### Paso 1: Crear el archivo `.ncdata` correcto
El archivo de seguridad debe llamarse obligatoriamente `.ncdata` y debe contener **exactamente una sola línea de texto**, finalizando con un salto de línea limpio.

Ejecutamos en la consola del host:
```bash
printf '# Nextcloud data directory\n' > /mnt/cloud-data/.ncdata



---
#### Referencias

