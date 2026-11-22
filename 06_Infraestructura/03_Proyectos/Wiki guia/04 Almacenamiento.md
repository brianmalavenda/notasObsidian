# Guía: Configuración de Almacenamiento, RAID y TrueNAS en Proxmox

Esta guía detalla la arquitectura de almacenamiento para el homelab, abriendo desde la configuración del RAID en el hipervisor hasta el despliegue de **TrueNAS** como máquina virtual principal de almacenamiento (NAS) y la exportación de carpetas compartidas para la administración de logs.

---

## 1. Arquitectura de Almacenamiento en Proxmox (RAID 1 Espejo)

Para garantizar la alta disponibilidad del sistema operativo base y la tolerancia a fallos ante la caída de un disco, la mejor práctica en Proxmox VE con dos discos idénticos es utilizar **ZFS en modo RAID 1 (Espejo)**.

### Ventajas de ZFS RAID 1 para el Homelab:

* **Integridad de datos (Self-Healing):** Detecta y repara automáticamente la corrupción de datos mediante *checksums* en tiempo real.
* **Redundancia:** Si un disco falla, el nodo sigue operativo en caliente; solo es necesario reemplazar el disco dañado y realizar un `zpool replace`.
* **Snapshots instantáneos:** Permite respaldar contenedores y VMs en milisegundos sin latencia perceptible.

---

## 2. Despliegue de TrueNAS como Máquina Virtual (NAS)

Para gestionar volúmenes de gran capacidad, discos dedicados y carpetas compartidas avanzadas, se recomienda desplegar **TrueNAS CORE o SCALE** en una Máquina Virtual (KVM) dentro de Proxmox.

### Requisitos y Configuración de la VM en Proxmox:

1. **CPU y RAM:** Asignar al menos 2 núcleos y 4 a 8 GB de RAM dedicados (TrueNAS/ZFS requiere memoria para caché ARC).
2. **Controlador HBA / Passthrough (Recomendado):** Para que TrueNAS gestione directamente los discos de almacenamiento masivo sin la abstracción de Proxmox, se debe realizar un **PCI Passthrough** de la controladora SATA/SAS física (HBA en modo IT) directamente a la VM de TrueNAS.
3. **Red:** Asignar una interfaz de red dedicada en modo *Bridge* (ej. `vmbr0`) para acceso SMB/NFS.

---

## 3. Configuración de Carpetas Compartidas para Logs y Administración

Para que la máquina administradora o los operadores puedan acceder de forma centralizada a los registros de logs y diagnósticos de los discos, configuraremos un recurso compartido seguro en TrueNAS utilizando el protocolo **SMB (Server Message Block)** o **NFS**, restringido mediante permisos estrictos.

### Paso 1: Creación del Dataset en TrueNAS

1. Ingresar al panel web de TrueNAS.
2. Ir a **Storage** → **Pools** → Seleccionar el *Pool* de discos y crear un nuevo Dataset llamado `homelab-logs`.
3. Configurar los permisos de acceso (ACLs) para restringir la lectura exclusivamente al usuario o grupo de administración autorizado.

### Paso 2: Creación del Recurso Compartido (SMB Share)

1. Ir a **Sharing** → **Windows Shares (SMB)** → **Add**.
2. Seleccionar la ruta del dataset `homelab-logs`.
3. Marcar la opción de habilitar el recurso compartido y guardarlo.

### Paso 3: Montaje de la Carpeta de Logs en la Máquina Administradora

Para centralizar la lectura de logs desde la máquina de gestión (por ejemplo, una estación Linux o un contenedor de administración):

1. Instalar las utilidades de cliente SMB si no están presentes:
```bash
sudo apt update && sudo apt install cifs-utils -y

```

2. Crear un punto de montaje local:
```bash
sudo mkdir -p /mnt/homelab_logs
```

3. Montar la carpeta compartida de manera persistente editando `/etc/fstab` (o mediante credenciales seguras en un archivo oculto):
```bash
//<IP_DE_TRUENAS>/homelab-logs /mnt/homelab_logs cifs credentials=/etc/samba/credentials.conf,iocharset=utf8,uid=1000,gid=1000,file_mode=0660,dir_mode=0770 0 0
```

4. Verificar que el montaje sea exitoso:
```bash
sudo mount -a
df -h | grep homelab-logs
```