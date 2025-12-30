tipo::
tags::
fecha:: 2025-12-29
links::
links_externos:: [proxmox](https://www.youtube.com/watch?v=odIUHwPknj0) , [instalar tesla p4](https://www.youtube.com/watch?v=ZOVUjKExn0o)


# Planificación

Partimos que las necesidades que tenemos en este servidor van más alla de pruebas o investigación sino que disponibilizaremos 3 servicios (Almacenamiento en nube de datos, Streaming de video y Webs y aplicaciones propias) que alcanzarían a una cantidad máxima de 200 usuarios.

Luego de armar mi [[01_sanmartin901]] como primer prueba donde trabajamos con un sistema operativo Open Media Vault (OMV) y virtualizacion de servicios a través de docker, el paso siguiente será saltar a utilizar Proxmox como sistema de virtualizacion completa donde los recursos del kernel del host no sean compartidos como es el caso de Docker sino que cada servicio tenga sus propios recursos aisgnados para garantizar disponibilidad, escalabilidad y estabilidad.
        
Con **Proxmox (VMs)** nos aseguraríamos que un fallo en el servidor virtual de streaming (Jellyfin o Plex) no afecta al servidor virtual de almacenamiento en la nube (Nextcloud).
Otro tema que nos motiva a cambiar la arquitectura del servidor es la disponibilidad de recursos físicos que pueden ser aprovechados al máximo con una máquina virtual y no con contenedores. Se puede reservar RAM y CPU mínimas garantizadas para cada servicio equivalente a cada VM. Este es el caso de la necesidad de hacer "transcoding" en el servidor de streaming. Con Proxmox, nos permite hacer **PCIe Passthrough directo** a una VM dedicada para Jellyfin o Plex y la GPU es asignada con propiedad exclusiva de esa VM. Con Docker la GPU se comparte vía drivers de contenedor (nvidia-docker), 

Para la disponibilidad y resiliencia, proxmox permite hacer **snapshots completos** de VMs (incluyendo SO, configuraciones, todo). Cuenta con la posibilidad de **Backups integrados** a nivel de hipervisor. Se puede realizar una **Migración en caliente** (si agregas otro servidor en el futuro) por necesidad de escalar. 

Además para actualización de los equipos virtuales podríamos hacer una copia o utilizar el último snapshot, actualizar los sistemas y verificar que todo este andando correctamente. Si algo se rompe podemos intentar arreglarlo y modificar o borrar y partir de una nueva foto hasta garantizar que la actualización no afecte los equipos garantizando la disponibilidad 100%.
        
Cada servidor virtual es aislado, quiere decir que el mantenimiento o error que pueda ocurrir en el streaming no afectará a la nube.

```text
HARDWARE (X99, 64GB RAM, Tesla P4)
    |
PROXMOX VE (en NVMe RAID1)
    |-----------------------------------|-------------------------------|
Virtualización de procesos      Servidor virtual 1              Servidor virtual 2
    ↓                                   ↓                               ↓
**LXC para Docker Host**           **VM para Jellyfin**           **VM para Nextcloud**
(2-4 cores, 8GB RAM)              (4-6 cores, 8GB RAM)           (4-6 cores, 12GB RAM)
    |                                   |  (PCIe Passthrough Tesla P4)   |
    ↓                                   ↓                               ↓
- Portainer                         - Jellyfin directo              - Nextcloud nativo
- WordPress contenedores            - GPU dedicada                  - Redis cache
- Aplicaciones propias              - Monta /storage-pool/media     - Monta /storage-pool/nextcloud-data
- Reverse Proxy (Traefik)           via NFS                         via NFS
- Base de datos MySQL
```


## Configuración de cada servidor virtual

**Distribución de recursos sugerida (de tus 64GB totales):**

Proxmox Host: 4GB reservados
LXC Docker: 8GB (con límite de 10GB)
VM Jellyfin: 8GB (6GB garantizados)
VM Nextcloud: 12GB (10GB garantizados)

**TOTAL USADO: 32GB**
DISPONIBLE: 32GB (para cache ZFS, crecimiento futuro, o nuevas VMs)

```text
PROXMOX (Host) - ZFS storage-pool (16TB RAID1)
│
├── LXC: "docker-host" (Ubuntu 22.04)
│   ├── 🐳 Traefik (proxy, SSL)
│   ├── 🐳 Portainer (gestión)
│   ├── 🐳 MariaDB (bases de datos)
│   ├── 🐳 WordPress 1, 2, 3...
│   └── 🐳 Tus apps personalizadas
│
├── VM: "jellyfin-server" (Ubuntu 22.04)
│   ├── 🎬 Jellyfin nativo
│   ├── 🎮 Tesla P4 dedicada
│   └── 📁 Monta /storage-pool/media (NFS)
│
└── VM: "nextcloud-server" (Ubuntu 22.04)
    ├── ☁️ Nextcloud nativo (LEMP stack)
    ├── 🚀 Redis cache
    └── 📁 Monta /storage-pool/nextcloud-data (NFS)
```


### Servidor 1: LXC Docker Host (El "Swiss Army Knife")

```yaml
Nombre: docker-host
Tipo: LXC Privilegiado (necesario para Docker dentro de LXC)
OS Template: ubuntu-22.04-standard
Recursos:
  - CPU: 4 cores (con límite)
  - RAM: 8GB (6GB garantizados)
  - Disco: 32GB (en NVMe para contenedores rápidos)
  - Red: Bridge vmbr0, IP estática
  - Nucleo: 4

Contenedores Docker que alojará:
1. Actualizar: `apt update && apt dist-upgrade`
2. traefik (reverse proxy con Let's Encrypt)
3. Crear un LXC privilegiado con Debian/Ubuntu.
4. Instalar Caddy: `apt install -y debian-keyring debian-archive-keyring apt-transport-https curl && curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg && ...`
5. Configurar `Caddyfile`:
	nginx
	media.tudominio.com {
		reverse_proxy 192.168.1.40:8096 # IP de Jellyfin
	}
	cloud.tudominio.com {
		reverse_proxy 192.168.1.30:80 # IP de Nextcloud
	}
6. Caddy obtiene y renueva SSL automáticamente.
7. portainer (gestión de Docker)
8. mysql/mariadb (base de datos central)
9. wordpress-1, wordpress-2, etc.
10. aplicaciones-custom (tus apps)
11. uptime-kuma (monitoreo)
12. Crear un `filesystem` en el almacenamiento de 16TB (ej: `mkfs.ext4 /dev/mdX`) y montarlo en `/mnt/storage`. Agregarlo a `/etc/fstab`.
13. Instalar `nfs-kernel-server` para compartir `/mnt/storage/nextcloud` y `/mnt/storage/media`.

Ventajas:
- Un solo lugar para gestionar todas las apps web
- Compartición eficiente de recursos entre contenedores
- Backup único del LXC = backup de todas las apps
```  

### Servidor 2: VM Jellyfin (La "Máquina de Video")

```yaml
Nombre: jellyfin-server
Tipo: VM completa
OS: Ubuntu Server 22.04 LTS (minimal install)
Recursos:
  - CPU: 6 cores (para transcodificación múltiple)
  - RAM: 8GB (con 2GB para GPU buffer)
  - Disco: 50GB SSD (sistema)
  - GPU: Tesla P4 via PCIe Passthrough
  - Red: Bridge vmbr0, IP estática
  - Nucleo: 6

Configuración especial:
1. PCIe Passthrough de la GPU (configurar en Proxmox primero)
2. Montar NFS del storage-pool/media
3. Instalar Jellyfin desde repositorio oficial
4. Identificar el GPU: `lspci -nn | grep NVIDIA`
5. Configurar transcodificación hardware con la Tesla P4
6. Habilitar IOMMU: Agregar `intel_iommu=on` (o `amd_iommu=on` si fuera AMD) en `/etc/default/grub` y actualizar. Conecta la biblioteca multimedia desde el almacenamiento masivo (NFS/SMB).
7. Aislar el dispositivo para el driver `vfio-pci` (agregar IDs en `/etc/modprobe.d/vfio.conf`).
```

### Servidor 3: VM Nextcloud (La "Nube")

```yaml

Nombre: nextcloud-server
Tipo: VM completa  
OS: Ubuntu Server 22.04 LTS
Recursos:
  - CPU: 6 cores (para concurrencia de usuarios)
  - RAM: 12GB (8GB para PHP-FPM, 2GB para Redis, 2GB sistema)
  - Disco: 100GB SSD (sistema + aplicaciones)
  - Disco adicional: 100GB+ (opcional para datos locales cache)
  - Red: Bridge vmbr0, IP estática
  - Nucleo: 6

Configuración:
1. Stack LEMP: Nginx + PHP 8.1 + MariaDB + Redis
2. Montar NFS del storage-pool/nextcloud-data, ver tambien SAMBA
3. Configurar Redis para caching y file locking
4. Tuning de PHP-FPM para 200 usuarios
5. SSL via reverse proxy (Traefik en Servidor 1)

```


## Instalación

Con **200 usuarios**, el desafío no es el procesamiento bruto, sino la **concurrencia** (cuántos entran al mismo tiempo) y el **ancho de banda**.

1. Instala Proxmox en el RAID 1 de NVMe. Usará una porción para su sistema y el resto lo dejará como `local-lvm` (almacenamiento rápido para discos de VM) y `local` (para ISOs, templates)  
2. Configurá el espejo de **HDDs** como un pool ZFS separado (llamalo `DATA`).
3. Usá "Mount Points" para pasar carpetas del pool `DATA` directamente a los contenedores LXC de Nextcloud y Jellyfin. Esto es mucho más rápido que usar protocolos de red internos.

## Red y seguridad

- Una NIC para gestión (proxmox) y, si es posible, una segunda para el tráfico de las VMs/Contenedores (mejora el rendimiento y la seguridad).
- **NO expongas la IP de administración de Proxmox (p. ej., 192.168.1.10:8006) a Internet.**
- Implementá **Fail2Ban** o **CrowdSec** en tu Reverse Proxy para bloquear intentos de ataque de fuerza bruta.
- Implementa una **VM o LXC dedicada como Reverse Proxy/Firewall**. **Usa OPNsense (VM) o Traefik/Caddy (LXC)**. Esta será la puerta de entrada (p. ej., en `192.168.1.20`).
    
- Todas tus apps (Nextcloud, Jellyfin, WordPress) correrán en IPs privadas (p. ej., `192.168.1.30-50`) y solo el reverse proxy estará expuesto (vía reglas de puerto en tu router físico).
    
- El proxy manejará los certificados SSL/TLS (Let's Encrypt) y el enrutamiento (`cloud.tudominio.com` -> Nextcloud, `media.tudominio.com` -> Jellyfin, etc.).


No expongas cada servicio a internet directamente. Necesitás un **Reverse Proxy**.
Un LXC con **Nginx Proxy Manager** o **Traefik**.
**Función:** Recibir el tráfico web (puertos 80/443), gestionar certificados SSL (Let's Encrypt) y derivar a `nube.tuempresa.com` o `cine.tuempresa.com`.
    
- **Ancho de banda de subida:** Para Nextcloud y Jellyfin, tu velocidad de subida a internet será el cuello de botella. Para 200 usuarios, necesitarías idealmente una conexión simétrica de fibra óptica (mínimo 300-500 Mbps).

## Backups

 Usa la herramienta de backup de Proxmox programada. Haz backup de las configuraciones de las VMs/LXC y, **críticamente**, de los **datos de aplicación** (base de datos de Nextcloud/WordPress, configuraciones, etc.). El almacenamiento masivo (16TB) debe tener su propia estrategia de backup (ej: `rsync` a otro NAS externo).
Revisar la herramienta  Rclone + scripts personalizados.

## Monitorización

Instalar algo simple como **Uptime Kuma** en un LXC para recibir alertas si un servicio se cae. En el host, usa `htop`, `glances` o `netdata`.
Grafana + Prometheus + Node Exporter + Uptime Kuma.

## Performance

Simular 50-100 conexiones concurrentes a Nextcloud y streams de transcodificación en Jellyfin. Monitorizar el uso de RAM, CPU e IO del disco.

## Documentación
   
Cada paso, cada IP, cada contraseña (en un gestor seguro), cada configuración especial. Te salvará en un desastre.
   

## Arquitectura

### Gateway

- **Mini PC:** de bajo consumo con 2-4 puertos NIC. 
- **Software:** **OPNsense** o **pfSense** (yo prefiero OPNsense por su interfaz más moderna).
    - **Firewall Stateful:** Control granular de qué IP/ puertos pueden hablar con qué.
    - **IDS/IPS (Suricata):** Detecta y bloquea intrusiones en tiempo real.        
    - **DNS Caching + Bloqueador (Unbound + bloqueo de dominios):** Puede hacer el 80% del trabajo del Pi-hole, descargándolo. El Pi-hole queda para ajustes finos y estadísticas.
    - **VPN Server (WireGuard/OpenVPN):** **Aquí es donde debe correr tu servidor VPN de entrada.** Es más seguro y eficiente. La Pi solo sería cliente si necesitas una VPN saliente.

#### Monitoreo

1. **Raspberry - Pi-hole Primario/ Secundario:**
    - Configúrala como **DNS recursivo local** (usando `unbound`) que consulta a los root servers, o como resolutor que apunta al DNS de OPNsense.
    - Su interfaz web es excelente para ver estadísticas de bloqueo y gestionar listas.
        
2. **Servidor de Monitoreo Ligero:**
    - Instala **Uptime Kuma** para monitorear tus servicios internos.
    - Puede correr un agente de **Prometheus Node Exporter**.
        
3. **Controlador de Red:**
    - **Home Assistant** si tienes IoT.
    - Servidor **NetData** para dashboards rápidos.
        
4. **Cliente VPN (Opcional):**
    - Si quieres que TODO el tráfico de tu red salga por una VPN comercial (ej., para descargas), puedes poner el cliente VPN aquí.

---
#### Referencias

