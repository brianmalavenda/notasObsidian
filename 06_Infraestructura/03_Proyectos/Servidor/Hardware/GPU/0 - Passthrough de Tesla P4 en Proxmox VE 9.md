tipo::
tags:: [proxmox], [gpu-passthrough], [nvidia], [tesla-p4]
fecha:: 2026-07-23
links::
links_externos::

> Guía basada en una implementación real sobre ASRock X99 Taichi + Xeon E5-2680 v4, Proxmox VE 9.1.9, con Tesla P4 + GeForce GT 1030 conviviendo en el mismo host. Documenta cada punto de conflicto encontrado durante el proceso, no solo el camino feliz.

## Contexto del hardware/software de referencia

- **Placa madre:** ASRock X99 Taichi (chipset de consumo/prosumer, sin IPMI)
- **CPU:** Intel Xeon E5-2680 v4 (VT-d)
- **GPU passthrough:** NVIDIA Tesla P4 (GP104GL, Pascal, datacenter)
- **GPU secundaria (host):** GeForce GT 1030 (video local del server)
- **Hipervisor:** Proxmox VE 9.1.9, bootloader GRUB clásico (no systemd-boot)
- **VM guest:** Debian 13 (Trixie), q35 + OVMF (UEFI)
- **Objetivo final:** Jellyfin en Docker con transcoding NVENC/NVDEC  

## Menú de puntos de conflicto

- **Informe 1** - [[1 - Preparación del host para GPU]] - puntos 1 a 7
- **Informe 2 - [[2 - Diagnóstico de incidentes en cascada y creación de la VM]] - puntos 8 a 13
- **Informe 3 - [[3 - NVIDIA en la VM]] -puntos 14 a 25

### Parte 1 — Preparación del host Proxmox

1. Activar IOMMU/VT-d: BIOS vs. cmdline del kernel (GRUB vs. systemd-boot)
2. Diferencia entre editar `/etc/default/grub` y que el cambio realmente aplique (`update-grub`)
3. Verificar que IOMMU quedó activo: `dmesg`, `/proc/cmdline`, conteo de IOMMU groups
4. Aislar la GPU del host: blacklist de nouveau/nvidia + binding a `vfio-pci`
5. Confirmar el IOMMU group de la tarjeta (aislada vs. compartida — cuándo hace falta ACS override)
6. Gestionar el Resource Mapping de Proxmox (Datacenter → PCI Devices) y por qué usarlo en vez de bus crudo
7. Riesgo de renumeración de bus PCI tras cambios de BIOS/cmdline, y cómo el mapping lo absorbe

### Parte 2 — Errores operativos y diagnóstico bajo presión

8. Cómo diferenciar un fallo real del host de un simple timeout de la shell web de Proxmox (noVNC/termproxy)
9. Efecto cascada: por qué un solo cambio (IOMMU) puede tumbar storages, LXCs y VMs que no tienen relación aparente
10. Diagnóstico de arranque cuando no hay IPMI: uso de consola física, journalctl, y orden de verificación  

### Parte 3 — Creación de la VM para passthrough

11. Parámetros correctos de VM para passthrough: `q35` + `OVMF` + PCI Express
12. Diferencia entre LXC y VM para acceso a GPU (por qué un LXC no sirve para este enfoque)
13. Agregar el PCI device correctamente vía Mapped Device (no Raw Device) y el flag `pcie=1`  

### Parte 4 — Instalación del driver NVIDIA dentro de la VM (la parte más conflictiva)

14. Aclaración de licenciamiento: por qué passthrough completo NO requiere licencia vGPU/suscripción NVIDIA
15. Elegir la rama correcta de driver para una GPU Pascal datacenter (qué ramas siguen soportando la P4)
16. Conflicto con el driver `nouveau` cargado por defecto en la VM guest
17. Error "Failed CC sanity check" / "No space left on device": el problema real de `/tmp` en `tmpfs`
18. Cómo diagnosticar logs de instalación de NVIDIA (`/var/log/nvidia-installer.log`) para separar ruido de errores reales
19. Secure Boot en OVMF bloqueando la carga de módulos de kernel sin firmar — diagnóstico y solución
20. Navegar el menú de firmware TianoCore/OVMF (Device Manager → Secure Boot Configuration) sin confundirlo con GRUB
21. Verificación final: `nvidia-smi` dentro de la VM

### Parte 5 — Jellyfin + NVENC (pendiente de documentar tras validar en producción)

22. Instalación de Docker + NVIDIA Container Toolkit dentro de la VM
23. Configuración de `docker-compose.yml` con `runtime: nvidia`
24. Activación de NVENC/NVDEC en Jellyfin (Dashboard → Playback → Transcoding)
25. Validación de transcoding por hardware en un stream real

---


