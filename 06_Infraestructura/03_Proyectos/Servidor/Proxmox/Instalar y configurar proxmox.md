tipo::
tags::
fecha:: 2026-03-26
links::
links_externos:: [GUIA](https://pve.proxmox.com/pve-docs/pve-admin-guide.html)


https://www.youtube.com/watch?v=sw-JuDbxV7A

Configuracion muy detallada de cada instalacion en Proxmox
https://www.youtube.com/watch?v=wNQ12uVFxyc

https://www.youtube.com/watch?v=YXoz6j9VBEE


### PVE Post install
https://community-scripts.org/scripts?q=post
```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
```

### PVE CPU Scalling Governor

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/scaling-governor.sh)"
```

Esto no lo se configurar

### PVE Kernel Clean

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/kernel-clean.sh)"
```

Ayuda a eliminar fallos de seguridad o rendimiento o actualizar archivos que una distribución mas nueva de proxmox haya modificado el kernel del sistema.

### PVE Processor Microcode

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/microcode.sh)"
```

Esto puede ser que actualice alguna configuración del sistema. Al terminar de ejecutarse, hay que correr esta línea ```journalctl -k | grep -E "microcode" | head -n 1``` para saber si hubo actualizaciones.

### ProxMenux

https://github.com/MacRimi/ProxMenux
```bash
bash -c "$(wget -qLO - https://raw.githubusercontent.com/MacRimi/ProxMenux/main/install_proxmenux.sh)"
```

Esto instala la opcion de tener los helpers scripts dentro de proxmox pero además crea una instancia de monitoreo accesible en la misma subred del proxmox en el puerto 8008.

![[PVE_Monitor.png]]


### ¿Cómo defino la IP en Proxmox?

La IP de Proxmox se define en el archivo de configuración de red del sistema (Debian). Puedes hacerlo desde la interfaz web (GUI) antes de cambiar las VLANs, o por consola.

**Pasos por consola (SSH o monitor directo):**

1. Editas el archivo: `nano /etc/network/interfaces`
2. Buscas la sección de tu puente principal (usualmente `vmbr0`).
3. Cambias la dirección:    
    ```
    address 192.168.10.10/24 # la subred de la VLAN de administracion para acceder desde PC_ADM
    gateway 192.168.10.1  # La IP de tu Router TP-Link
    ```
4. Reinicias la red: `systemctl restart networking`

# Reconocimiento de periféricos PCIe - IOMMU


# Network configuration

Jumbo frames

---
#### Referencias
