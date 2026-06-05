tipo::
tags::
fecha:: 2026-04-14
links::
links_externos::

## Política de Backups

1. **Producción:** Las apps corren en el NVMe (B1) y sus datos duros de almacenamiento van al Mirror de 4TB (A1/A2).    
2. **Backup Diario Inteligente (PBS):** Proxmox le envía todas las noches copias incrementales al DISCO C1 de la Netbook que es mi servidor PBS. Esto te protege contra fallas del servidor principal, borrados accidentales y ransomware.
3. **Resguardo Offline / Desastre Total:** Configura un script sencillo en tu `vm-admin` para que una vez por semana comprima la configuración de Proxmox (`/etc/pve`) y un backup plano de TrueNAS, y lo deposite en tu carpeta `share_data`. Desde tu PC ADMIN (DISCO D1), entras por SMB y te descargas ese archivo a tu computadora local.

**SERVIDOR PROXMOX PVE**
 ├── DISCO B1 (1TB NVMe): Sistema Operativo PVE + Discos de VMs (vm-admin, vm-apps, WordPress).
 ├── DISCO B2 (1TB NVMe): ¡Úsalo como almacenamiento "ZFS Local-Backup" o réplica local!
 └── DISCOS A1 y A2 (4TB Mirror): Almacenamiento exclusivo de la DATA DURA (Archivos de Nextcloud, share_data) gestionado por TrueNAS en Passthrough.

**SERVIDOR PBS (Netbook)**
 └── DISCO C1 (1TB Mecánico/SSD): Datastore exclusivo de Chunks deduplicados de PBS.

**PC ADMIN**
 └── DISCO D1 (1TB SSD): Copias "en frío" semanales (.tar.zst) descargadas de la carpeta share_data. La carpeta share_data es un un recurso compartido en TrueNAS. En una PC con Windows, ese recurso se comparte por **SMB**. 

### Respaldar información de Nextcloud

Para asegurarte de que el PBS (DISCO C1) realmente salve los datos de tus usuarios de Nextcloud, configura la VM de Nextcloud de esta manera:

- Crea el disco virtual del sistema de Nextcloud en el **DISCO B1**.
    
- Añade un **segundo disco virtual** a la VM de Nextcloud, pero ubícalo físicamente dentro del almacenamiento NFS que te provee TrueNAS (los discos **A1/A2**).
    
- **¿Por qué?** De esta forma, los datos de los usuarios se escriben físicamente en el Mirror de 4 TB de TrueNAS (protegidos contra fallas de hardware), pero al ser un "disco virtual" controlado por Proxmox, cuando el PBS corre el backup diario, **sí va a poder respaldar esos 4 GB de datos de usuario** hacia el DISCO C1 de la netbook.


![[Pasted image 20260414044426.png]]
### Paso 1: El Secreto para un Backup Seguro

Antes de nada, un consejo de experto para tu homelab: **no compartas el dataset principal de backups**. Si un ransomware infecta tu PC y ves la unidad de backups, la encriptará también[](https://www.truenas.com/docs/core/13.0/coretutorials/sharing/smb/managingsmbshares/#acl-presets).

En lugar de eso, crea un **dataset específico para "inspección"**. Usarás un snapshot, que es como una foto instantánea e inmutable de tus datos en un momento dado[](https://cdn.truenas.com/docs/scale/24.10/gettingstarted/configure/setupbackupscale/).

**Así se hace (desde la interfaz de TrueNAS):**

1. Ve a `Storage` > `Pools` y selecciona tu pool de backups.
    
2. Haz clic en `Add Dataset`. Nómbralo, por ejemplo, `backup_inspect`.
    
3. Ve a `Data Protection` > `Periodic Snapshot Tasks` y añade una tarea para que tome snapshots de tu dataset de backup real de forma automática[](https://cdn.truenas.com/docs/scale/24.10/gettingstarted/configure/setupbackupscale/).
    
4. Cuando quieras ver los archivos desde tu PC, ve a `Storage` > `Snapshots`, busca el snapshot más reciente, haz clic en `Clone` y asígnale el dataset `backup_inspect` como destino.
    
5. Luego, compartes ese clone `backup_inspect` vía SMB. Cuando termines, simplemente eliminas el clone.
    

### Paso 2: Preparando el Dataset para Compartir (SMB)

Una vez tengas claro el paso 1, prepara el terreno en TrueNAS.

1. **Ve a `Storage` > `Pools`** y localiza el dataset que vas a compartir (por ejemplo, `backup_inspect` o tu dataset final).
    
2. Haz clic en los tres puntos (`more_vert`) a la derecha y selecciona `Edit Permissions`.
    
3. Aquí es donde controlas quién puede ver qué. Para simplificar y que tu usuario de Windows pueda acceder, puedes **seleccionar el preset `OPEN`**[](https://www.truenas.com/docs/core/13.3/gettingstarted/sharingstorage/#accessing-the-dataset). Esto abrirá los permisos para que los usuarios que se autentiquen puedan acceder.
    
4. Haz clic en `Save`.
    
### Paso 3: Creando el Recurso Compartido (SMB)

Ahora sí, vamos a crear el share propiamente dicho.

1. Ve a `Sharing` > `Windows Shares (SMB)` y haz clic en `ADD`[](https://www.truenas.com/docs/core/13.3/gettingstarted/sharingstorage/#accessing-the-dataset)[](https://www.truenas.com/docs/core/13.3/coretutorials/sharing/smb/).
    
2. En `Path`, busca y selecciona el dataset que preparaste (`backup_inspect`).
    
3. En `Name`, asígnale un nombre corto y sin espacios, como `BackupMonitor`[](https://www.truenas.com/docs/core/13.3/coretutorials/sharing/smb/).
    
4. El resto de opciones déjalas por defecto. Haz clic en `SUBMIT`.
    
5. Si el servicio SMB no está activado, TrueNAS te preguntará si quieres iniciarlo. Acepta y actívalo para que arranque siempre con el sistema[](https://www.truenas.com/docs/core/13.3/gettingstarted/sharingstorage/#accessing-the-dataset).
    

### Paso 4: Conectándote desde tu PC Windows

¡Ya está todo listo! Conectarte es tan fácil como acceder a una carpeta de la red.

1. Abre el **Explorador de Archivos** en tu PC.
    
2. En la barra de direcciones, escribe la ruta de tu servidor TrueNAS. Puede ser su nombre o su IP: `\\nombre-o-ip-de-tu-truenas` (Ejemplo: `\\192.168.1.10`)[](https://www.truenas.com/docs/core/13.3/gettingstarted/sharingstorage/#accessing-the-dataset).
    
3. Te pedirá usuario y contraseña. Usa las credenciales de un usuario que hayas creado en TrueNAS (en `Accounts` > `Users`). Asegúrate de que ese usuario tenga permisos sobre el dataset que compartiste.
    

### Resumen Visual de tu Flujo de Trabajo


---
#### Referencias

