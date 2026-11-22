## 1. ¿Por qué elegir Proxmox VE?

Para un homelab que busca escalar (soportando cargas web exigentes de hasta 200 usuarios concurrentes), utilizar un hipervisor Bare-Metal de Tipo 1 ofrece ventajas críticas:
- **Base sólida en Debian 12 (Bookworm):** Hereda la estabilidad, el ecosistema de paquetes y la seguridad de Debian, permitiendo usar herramientas estándar (`nftables`, `systemd`, `iproute2`).
- **Dualidad nativa (KVM + LXC):** Permite correr máquinas virtuales completas (KVM) para aislamiento total, y contenedores ligeros LXC (con una fracción de RAM/CPU), ideales para microservicios web eficientes.
- **Gestión de almacenamiento avanzada:** Soporte nativo para ZFS, LVM y Ceph.
- **Alta disponibilidad y respaldos:** Integración nativa con Proxmox Backup Server (PBS) y snapshots automáticos.
## 2. Instalación de Proxmox VE desde Cero

### Paso 1: Requisitos previos y medio de arranque

- **Hardware mínimo:** CPU con virtualización por hardware (Intel VT-x / AMD-V), mínimo 16 GB de RAM (recomendado ECC o 32GB+ para 200 usuarios) y al menos dos discos (para ZFS o separación OS/Datos).
- **Descarga:** Obtener la ISO oficial de **Proxmox VE Installer ISO**.
- **USB Booteable:** Quemar la imagen utilizando **Ventoy** o **Rufus** (en modo `DD`).

### Paso 2: Proceso de instalación (Asistente gráfico)

1. Arrancar el equipo desde el pendrive e iniciar **Install Proxmox VE**.
2. **Selección del disco de destino (`Target HardDisk`):**
    - _Recomendación:_ Si usas dos discos iguales, configurar el sistema de ficheros como **ZFS (RAID 1)** para tolerancia a fallos. Si usas un solo disco, seleccionar `ext4` o `XFS` con LVM.
3. **Localización:** Seleccionar país, zona horaria y distribución de teclado.
4. **Credenciales:** Definir una contraseña segura para `root` y un correo electrónico de alertas.
5. **Configuración de Red:**
    - **Management Interface:** Seleccionar la interfaz física principal (ej. `eno1` o `eth0`).
    - **Hostname:** Asignar un FQDN (ej. `pve01.homelab.local`).
    - **IP Address / Gateway / DNS:** Indicar una IP estática dentro de la subred de administración del homelab y la puerta de enlace del router.

## 3. Primer Ingreso y Configuración Post-Instalación

Una vez finalizada la instalación y reiniciado el equipo, acceder desde el navegador web a:`https://<IP_DE_PROXMOX>:8006` e iniciar sesión con el usuario `root`.

### Scripts de Post-Instalación Recomendados (PVE Post-Install)

Para optimizar el nodo (desactivar el repositorio nag de suscripción comercial, configurar repositorios `no-subscription`, actualizar fuentes y aplicar ajustes de rendimiento del kernel), se recomienda utilizar el script automatizado estándar de la comunidad (PVE Helper-Scripts):

1. Ingresar por SSH al nodo Proxmox como `root`.
2. Ejecutar el script interactivo de post-instalación:
    
    ```bash
    -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/misc/post-install.sh)"
    ```
    
1. Seguir las opciones en pantalla para:
    - Deshabilitar el aviso molesto de suscripción (_No-Subscription nag_).
    - Agregar y actualizar los repositorios correctos de Debian 12 / Proxmox VE.
    - Habilitar actualizaciones automáticas de seguridad si se desea.

## 4. ProxMenux: Menú Interactivo, Scripts y Dashboard Web

**ProxMenux** es un conjunto de herramientas todo-en-uno diseñado específicamente para simplificar la administración de Proxmox VE mediante un menú visual en consola y un panel web moderno (**ProxMenux Monitor**).
### Características principales

- **Menú guiado por terminal (`menu`):** Permite ejecutar tareas de post-instalación, configurar GPU Passthrough, gestionar discos/almacenamiento, configurar redes y lanzar scripts de la comunidad sin recordar comandos complejos.
- **Asistentes de Backup y Restauración:** Facilita la creación de respaldos y configuración de almacenamiento externo.
- **ProxMenux Monitor (Dashboard Gráfico):** Se instala automáticamente junto con ProxMenux y levanta una interfaz web con gráficas en tiempo real de CPU, RAM, discos, tráfico de red, estado de VMs/LXC y terminal WebSocket integrada.

### Instalación de ProxMenux

1. En la terminal del nodo Proxmox (como `root`), ejecutar el instalador oficial:
    ```bash
    bash -c "$(wget -qLO - https://raw.githubusercontent.com/MacRimi/ProxMenux/main/install_proxmenux.sh)"
    ```
2. El instalador configurará automáticamente las dependencias necesarias (`dialog`, `jq`, `git`, `python3`, etc.).
3. Una vez finalizado, iniciar el menú interactivo en cualquier momento escribiendo:
    ```bash
    menu
    ```
    _(En el primer inicio te permitirá elegir el idioma de la interfaz, incluyendo Español)_.
