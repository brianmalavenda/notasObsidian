tipo::
tags::
fecha:: 2026-06-05
links::
links_externos::
entorno:: Proxmox VE (PVE) `sm-serv700` ➡️ Proxmox Backup Server (PBS) en Netbook Dedicada  

## 1. Resumen del Incidente

Durante la ventana de mantenimiento automatizada de las **03:00 AM**, el Job de backup general configurado a nivel de Datacenter en Proxmox VE falló de forma masiva, generando un **efecto dominó (reacción en cadena)** que interrumpió el resguardo de todas las instancias del nodo (`VM 100`, `VM 101`, `VM 102`, `LXC 200` y `LXC 201`).

### 📑 Log de Error Crítico Original
```text
INFO: Starting Backup of VM 100 (qemu)
INFO: include disk 'sata1' '/dev/disk/by-id/ata-WDC_WD40EFPX...' 3.9 TB
...
ERROR: VM 100 qmp command 'query-backup' failed - got timeout
INFO: Starting Backup of VM 101 (qemu)
ERROR: Backup of VM 101 failed - storage 'hdd-cloud-stg' is not online
INFO: Starting Backup of VM 102 (qemu)
ERROR: Backup of VM 102 failed - storage 'hdd-cloud-stg' is not online
INFO: Starting Backup of VM 200 (lxc)
INFO: Error: client error (Connect)
ERROR: Backup of VM 200 failed - command 'lxc-usernsexec ...' failed: exit code 255
```

## 2. Diagnóstico Técnico y Análisis de Causa Raíz

El análisis forense de los logs reveló que el fallo no se debió a problemas independientes en cada VM, sino a un **bucle lógico de codependencia de almacenamiento** combinado con **cuellos de botella físicos (I/O y Red)**.

### 🔍 Desglose de la Reacción en Cadena:

1. **El Detonante (VM 100 - TrueNAS Core/Scale):** La tarea fue configurada erróneamente en modo `All Guests`. El proceso inició intentando realizar un Snapshot en caliente de la **VM 100 (TrueNAS)**, la cual gestiona un passthrough físico de discos mecánicos en RAID (`sata1` y `sata2`) que totalizan **7.3 TiB**. El comando QMP (`query-backup`) del hipervisor excedió el tiempo límite de espera (**Timeout**) debido a la saturación extrema de operaciones por segundo (IOPS) al intentar indexar semejante volumen de datos en discos mecánicos.
    
2. **Colapso del Storage Compartido (`hdd-cloud-stg`):** Al congelarse la VM de TrueNAS por el timeout, el servicio NFS provisto por ella se interrumpió. Proxmox VE perdió instantáneamente el montaje de red `hdd-cloud-stg`. Cuando el Job pasó a respaldar las **VM 101 (vm-admin)** y **VM 102 (vm-apps)**, sus discos secundarios mapeados en ese almacenamiento NFS figuraban _Offline_, forzando el aborto inmediato de ambas tareas.
    
3. **Saturación del Adaptador de Red USB en el PBS:** Finalmente, las tareas de los contenedores LXC (200 y 201) fallaron con un error `client error (Connect)`. El hardware del PBS de destino es una netbook que **carece de placa de red integrada y utiliza un adaptador USB a RJ45**. El flujo masivo y descontrolado de datos inicial colapsó el búfer del driver USB o activó las políticas de ahorro de energía del kernel (`USB Autosuspend`), dejando la interfaz de red de la netbook en un estado "mudo" (unresponsive).
    

## 3. Arquitectura de Solución Propuesta

Para remediar esto, se diseñó e implementó un plan de acción en tres fases enfocado en la **estabilización del hardware** y la **optimización del flujo de trabajo en QEMU**.

### 🛠️ Fase 1: Corrección Lógica del Job (Romper el Bucle)

Se eliminó la VM de TrueNAS (`VM 100`) del esquema de copias de seguridad general. No se debe respaldar el hipervisor/origen de almacenamiento mediante el mismo canal que consume dicho almacenamiento.

- **Acción:** Se modificó el parámetro `Selection Mode` en _Datacenter -> Backup_ pasando de _All Guests_ a **`Selected Guests`**, incluyendo estrictamente a las VMs productivas (`101`, `102`, `200`, `201`).

### 🛠️ Fase 2: Mitigación de Hardware (Adaptador USB-Ethernet en PBS)

Para evitar que el adaptador USB del PBS colapse por estrés térmico o suspensión por software, se aplicaron dos contramedidas directo en la Shell del PBS:

1. **Desactivación Global del USB Autosuspend:** Se le ordenó al kernel de Debian que mantenga los puertos USB energizados al 100% de forma persistente:
    
    ```
    echo "options usbcore autosuspend=-1" > /etc/modprobe.d/usb-autosuspend.conf
    ```
    
1. **Script Watchdog de Auto-Recuperación (LAN):** Se implementó un script cronometrado (`/usr/local/bin/check-lan-network.sh`) que verifica la conectividad ICMP contra el router cada 10 minutos. Si detecta pérdida de enlace, fuerza proactivamente el reinicio del stack de red:

```bash
#!/bin/bash
TARGET_IP="10.0.17.1000"
# Hacemos 3 pings con un timeout de 5 segundos cada uno
if ! ping -c 3 -W 5 "$TARGET_IP" > /dev/null 2>&1; then
   echo "$(date): Red caída. Reiniciando interfaces..." >> /var/log/network-recovery.log
   systemctl restart networking
fi
```

```
# Configurado en crontab de la netbook:
*/10 * * * * /usr/local/bin/check-lan-network.sh
```
    

### 🛠️ Fase 3: Optimización de Parámetros Avanzados en PVE

Aprovechando las características de grano fino de las versiones modernas de Proxmox VE, se alteró la configuración avanzada del Job de Backup para adaptarla a las limitaciones físicas del entorno hogareño/homelab:

```
| Parámetro | Valor Configurado | Justificación Técnica |
| :--- | :--- | :--- |
| **Bandwidth Limit** | `50 MiB/s` | Limita la tasa de transferencia a ~400 Mbps para evitar que el chip del adaptador USB-RJ45 levante temperatura crítica y sufra desconexiones por hardware. |
| **Fleecing** | `Enabled` | Activa el búfer de escritura intermedia en caliente. |
| **Fleecing Storage**| `local-lvm` | Fuerza a QEMU a usar el almacenamiento sólido (NVMe Local) de la máquina principal como caché temporal si el PBS (netbook) se ralentiza, evitando el freeze de las VMs. |
| **IO-Workers** | `2` (o 3) | Reduce los hilos concurrentes de lectura sobre el pool NFS. Los discos mecánicos de TrueNAS procesan esto como lectura secuencial pura, eliminando latencias y timeouts. |
```

> [!NOTE] **¿Qué es el Fleecing exactamente?** > Cuando una VM está bajo backup en modo snapshot, las escrituras nuevas de los usuarios se congelan si el destino es lento. Con _Fleecing_ activo sobre el `local-lvm` (NVMe), los bloques modificados en producción se escriben temporalmente a velocidad de estado sólido local. QEMU se encarga luego de enviarlos de forma asíncrona al PBS, garantizando **cero caídas de servicio (I/O Freeze)** en aplicaciones críticas como Nextcloud.

## 4. Estado Actual y Próximos Pasos

> [!WARNING] **Pendiente de Validación Final** > Las políticas, restricciones de ancho de banda y el aislamiento del storage ya se encuentran operativos en producción. Resta monitorear el comportamiento del entorno durante la próxima ventana de mantenimiento automatizada (Mañana, 03:00 AM) para confirmar si las ráfagas incrementales del PBS se completan de manera exitosa y transparente.

#### Referencias

