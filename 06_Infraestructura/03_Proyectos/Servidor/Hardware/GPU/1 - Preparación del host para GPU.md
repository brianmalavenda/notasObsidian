
tipo:: 
tags:: [proxmox], [gpu-passthrough], [nvidia], [tesla-p4]
fecha:: 2026-07-23
links:: 
links_externos::


> Esta primera parte cubre todo lo que hay que dejar listo **en el hipervisor**, antes de tocar ninguna VM. Es la base sobre la que se apoya todo lo demás: si algo acá queda mal configurado, los síntomas van a aparecer recién más adelante (driver que no carga, VM que no arranca, GPU "fantasma" que no se puede pasar), y ahí es mucho más difícil diagnosticar la causa real.

## Contexto de referencia

- Placa: ASRock X99 Taichi (chipset de consumo, sin IPMI)
- CPU: Intel Xeon E5-2680 v4
- Proxmox VE 9.1.9, bootloader GRUB clásico
- GPU a pasar: NVIDIA Tesla P4 (`10de:1bb3`), en el bus PCI `05:00.0`
- GPU que se queda en el host: GeForce GT 1030 (video local del server)

---

## 1. Activar IOMMU/VT-d: por qué es el primer paso obligatorio

### El concepto

El **passthrough de PCI** consiste en darle a una VM acceso _directo_ y _exclusivo_ a un dispositivo de hardware físico, como si estuviera enchufado directamente en ella. Para que esto sea seguro, el hardware necesita un mecanismo que garantice que esa VM **no pueda usar ese dispositivo para leer o escribir en zonas de memoria RAM que no le corresponden** (por ejemplo, la memoria del host o de otras VMs).

Ese mecanismo se llama **IOMMU** (Input-Output Memory Management Unit). En procesadores Intel se implementa como **VT-d** (Virtualization Technology for Directed I/O). Sin IOMMU activo, Proxmox directamente no te va a dejar hacer passthrough de un dispositivo PCI a una VM — o si lo fuerza, el resultado es inestable e inseguro.

### Dos lugares donde hay que activarlo

Activar VT-d **no es un solo paso**, son dos, y si te salteás uno el otro no sirve de nada:

**a) En el firmware (BIOS/UEFI) de la placa madre.** Esto es una condición de hardware: si el firmware no expone la funcionalidad al sistema operativo, no hay nada que el kernel de Linux pueda hacer. En la ASRock X99 Taichi, esta opción suele estar en:

```
Advanced > CPU Configuration > Intel VT-d
```

Puede aparecer separada de "Intel Virtualization Technology" (que es **VT-x**, para virtualizar CPU — algo distinto, y que probablemente ya tengas activo si corrés VMs). VT-d es específicamente la que habilita el IOMMU para dispositivos PCI.

**b) En los parámetros de arranque del kernel Linux (cmdline).** Aunque el firmware lo soporte, el kernel de Linux no lo activa por defecto — hay que decírselo explícitamente vía parámetro de boot.

### Por qué importa saber qué bootloader usás

Acá aparece el primer punto donde la teoría genérica de "cualquier tutorial de Proxmox" te puede confundir: **Proxmox VE puede usar `systemd-boot` o GRUB clásico**, dependiendo de cómo fue instalado el sistema (por ejemplo, el modo de partición elegido en el instalador). Cada uno lee la configuración de un lugar distinto.

Para saber cuál tenés:

```bash
proxmox-boot-tool status
```

Si tu instalación usa **GRUB** (como en este caso), el parámetro va en `/etc/default/grub`:

```bash
nano /etc/default/grub
```

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"
```

- `intel_iommu=on` activa el IOMMU de Intel explícitamente.
- `iommu=pt` (passthrough mode) le dice al kernel que, para los dispositivos que **no** vas a pasar a una VM, use el modo de traducción más simple posible (identity mapping) en vez de aplicar traducción de direcciones completa. Esto evita una pequeña pérdida de rendimiento en el resto del sistema que no necesita el aislamiento estricto del IOMMU.

### El error más común en este paso

Editar `/etc/default/grub` **no aplica el cambio por sí solo**. Ese archivo es solo una plantilla; GRUB arranca leyendo `/boot/grub/grub.cfg`, que es un archivo generado. Si no lo regenerás, vas a reiniciar convencido de que activaste IOMMU y vas a seguir sin nada:

```bash
update-grub
```

Vas a ver que el comando lista todos los kernels instalados (Proxmox suele tener varios, de distintas actualizaciones) — no hace falta hacer nada más, `update-grub` los actualiza a todos con el nuevo cmdline automáticamente.

Si tu instalación usa `systemd-boot` en cambio (podés confirmarlo con el comando de arriba), el archivo a editar es distinto — `/etc/kernel/cmdline` — y el comando para aplicar el cambio es `proxmox-boot-tool refresh` en vez de `update-grub`. Vale la pena confirmar cuál tenés **antes** de buscar el archivo, para no perder tiempo buscando una ruta que no existe en tu sistema.

---

## 2. Verificar que IOMMU realmente quedó activo (no asumirlo)

Después de reiniciar, no des por sentado que funcionó — verificalo con evidencia concreta. Hay tres chequeos, cada uno te dice algo distinto:

### a) ¿El kernel arrancó con el parámetro?

```bash
cat /proc/cmdline
```

Tiene que aparecer `intel_iommu=on iommu=pt` en la línea. Esto confirma que el **bootloader** pasó el parámetro — pero no confirma todavía que el **hardware** lo haya aceptado.

### b) ¿El hardware reportó soporte de IOMMU al bootear?

```bash
dmesg | grep -e DMAR -e IOMMU
```

Si el firmware tiene VT-d activo y el kernel lo reconoció, vas a ver líneas como:

```
DMAR: IOMMU enabled
DMAR: Host address width 46
```

**Si este comando no devuelve nada**, hay dos causas posibles, y conviene revisarlas en este orden:

1. El cmdline no tiene el parámetro (volvé al punto `a`).
2. VT-d está deshabilitado en el BIOS, a pesar de que el software esté bien configurado. Esto puede pasar incluso después de haber "activado virtualización" en el BIOS, porque como se mencionó antes, VT-x y VT-d suelen ser opciones separadas.

### c) ¿Cuántos IOMMU groups existen?

```bash
ls /sys/kernel/iommu_groups/ | wc -l
```

Este número te da una idea de qué tan bien segmenta tu hardware los dispositivos PCI (más sobre esto en el punto 5). Un valor de `0` confirma que, aunque el cmdline esté bien, el IOMMU no está realmente operativo a nivel de hardware.

> **Nota de la experiencia real:** en este proceso, la placa ASRock X99 Taichi devolvió las tres verificaciones correctas recién en el segundo intento, porque el primer reboot había cargado un kernel viejo desde el menú de GRUB (el sistema tenía varias versiones instaladas) en el cual, aparentemente, no se aplicó bien la regeneración. Apagar completamente el equipo (no solo reboot) y volver a prender resolvió la inconsistencia. Si el chequeo `b` te sigue devolviendo vacío después de un `update-grub` correcto, probá un apagado completo antes de sospechar del BIOS.

---

## 3. Cargar los módulos VFIO

`vfio-pci` es el **driver genérico** que el kernel de Linux usa para "reservar" un dispositivo PCI y ponerlo a disposición de QEMU/KVM, en vez de dejar que lo tome el driver normal del dispositivo (en este caso, el driver gráfico). Sin este driver, no hay forma de aislar la tarjeta del resto del sistema.

Agregalo a la carga de módulos del kernel:

```bash
echo "vfio
vfio_iommu_type1
vfio_pci" >> /etc/modules
```

Estos tres módulos trabajan juntos: `vfio_iommu_type1` es el que efectivamente usa las tablas de traducción del IOMMU para aislar la memoria del dispositivo; `vfio_pci` es la interfaz concreta para dispositivos PCI.

---

## 4. Aislar la GPU del host: por qué hace falta bloquear otro driver primero

Este es un punto conceptualmente importante. **No alcanza con "activar" vfio-pci** — el kernel de Linux, al bootear, intenta cargar automáticamente el driver más apropiado para cada dispositivo PCI que detecta. Para una GPU NVIDIA, eso normalmente es `nouveau` (el driver open-source incluido en el kernel) o, si está instalado, el driver propietario `nvidia`.

Si cualquiera de esos dos toma la tarjeta primero, **vfio-pci no puede tomarla después** — un dispositivo solo puede tener un driver de kernel activo a la vez. Por eso hay que hacer dos cosas en conjunto:

### a) Decirle a `vfio-pci` qué dispositivo reservar, por su ID de hardware

Cada modelo de tarjeta tiene un ID único de vendor:device (no depende del bus PCI, que puede cambiar; depende del modelo de la tarjeta). Para encontrarlo:

```bash
lspci -nn | grep -i nvidia
```

En este caso: `NVIDIA Corporation GP104GL [Tesla P4] [10de:1bb3]`. El `10de:1bb3` es el ID que importa.

```bash
echo "options vfio-pci ids=10de:1bb3" > /etc/modprobe.d/vfio.conf
```

**Importante si tenés más de una GPU NVIDIA en el mismo host** (como en este caso, con la GT 1030 conviviendo): este archivo captura únicamente el ID de la Tesla P4. La GT 1030 (`10de:1d01` para la parte de video, `10de:0fb8` para su controlador de audio HDMI) queda libre para que el host la siga usando normalmente.

### b) Bloquear explícitamente que nouveau/nvidia tomen la tarjeta

```bash
echo "blacklist nouveau
blacklist nvidia
blacklist nvidiafb
blacklist nvidia_drm" > /etc/modprobe.d/blacklist-nvidia.conf
```

Este blacklist es **global** — bloquea esos drivers para _cualquier_ dispositivo NVIDIA del sistema, no solo la P4. En este caso puntual no generó conflicto porque la GT 1030 se usa únicamente para salida de video básica del host (no depende del driver 3D de NVIDIA para eso; el framebuffer genérico alcanza). Si tu caso depende de que el host use activamente el driver NVIDIA en la segunda tarjeta, este enfoque de blacklist global no te sirve tal cual — ahí se necesitaría una estrategia más quirúrgica (vincular el driver por bus PCI específico en vez de por nombre de driver), que queda fuera del alcance de esta guía.

### c) Aplicar los cambios

Los archivos en `/etc/modprobe.d/` no toman efecto hasta que se regenera la imagen de arranque temprano del sistema (initramfs), que es donde el kernel decide, en las primerísimas etapas del boot, qué driver le corresponde a cada dispositivo:

```bash
update-initramfs -u -k all
reboot
```

---

## 5. Confirmar el binding y el IOMMU group de la tarjeta

Después del reboot, verificá que el aislamiento haya funcionado:

```bash
lspci -k -s 05:00.0
```

El resultado tiene que mostrar:

```
Kernel driver in use: vfio-pci
```

Si en cambio no aparece ninguna línea de "Kernel driver in use", significa que **ningún** driver la tomó — es mejor que tenerla en nouveau, pero todavía no está lista. Generalmente indica un problema de orden de carga entre el blacklist y el vfio.conf, que se resuelve confirmando que ambos archivos existen y regenerando el initramfs de nuevo.

### Por qué el IOMMU group importa tanto como el driver

Acá hay un concepto que se pasa por alto seguido: el IOMMU no aísla dispositivos individuales, sino **grupos** de dispositivos. Dependiendo de cómo esté cableado el chipset de la placa madre a nivel de topología PCIe, dos o más dispositivos distintos pueden terminar compartiendo el mismo "IOMMU group" — y **Proxmox no puede pasar a una VM un solo dispositivo de un grupo sin pasar el grupo entero**, porque separarlos rompería la garantía de aislamiento que el IOMMU existe para dar.

Esto es especialmente relevante en placas de consumo/prosumer como la X99 Taichi, que no siempre segmentan tan bien los buses PCIe como el hardware de servidor dedicado.

Para ver en qué grupo cayó tu tarjeta:

```bash
readlink /sys/bus/pci/devices/0000:05:00.0/iommu_group
```

Esto devuelve algo como `../../../../kernel/iommu_groups/55`. Con ese número, mirá qué más hay en ese grupo:

```bash
ls /sys/kernel/iommu_groups/55/devices/
```

- **Si el grupo contiene solo tu GPU:** perfecto, es el escenario ideal, podés pasarla sin ninguna complicación adicional.
- **Si comparte grupo con otros dispositivos** que necesitás en el host (por ejemplo, un controlador SATA con tus discos, o un puerto USB con periféricos que usás localmente), tenés un problema: pasar la GPU obligaría a pasar también esos otros dispositivos.

La solución en ese caso es el **ACS override patch** (`pcie_acs_override=downstream,multifunction` agregado al cmdline), que fuerza al kernel a tratar ciertos dispositivos como si estuvieran en grupos separados aunque el hardware físicamente los agrupe. **Se usa con cautela**: técnicamente relaja una garantía de aislamiento que el IOMMU da por diseño, así que conviene probar primero sin él y activarlo solo si el chequeo de arriba confirma que realmente lo necesitás.

> **Resultado en este caso real:** la Tesla P4 en el bus `05:00.0` cayó en el **IOMMU Group 55, conteniendo únicamente ese dispositivo**. No hizo falta ACS override. Esto no es garantía de que tu configuración vaya a tener la misma suerte — depende de la topología específica de tu placa — pero confirma que no todas las placas de consumo tienen mal agrupamiento, vale la pena medir antes de asumir que vas a necesitar el override.

---

## 6. Usar Resource Mapping en vez de bus PCI crudo

Proxmox permite pasar un dispositivo PCI a una VM de dos formas: especificando el bus/slot crudo (ej. `hostpci0: 0000:05:00.0`) o usando un **mapping con nombre**, configurado a nivel Datacenter.

### Por qué conviene el mapping

El identificador de bus PCI (`05:00.0`) **no es una propiedad fija de la tarjeta** — es una posición que el firmware/kernel le asigna en cada boot, en función de qué otros dispositivos hay conectados y en qué orden se enumeran. Cambios aparentemente no relacionados (actualizar el BIOS, activar/desactivar VT-d, incluso a veces una actualización de kernel) pueden hacer que ese número cambie.

Si tenés `hostpci0: 0000:05:00.0` hardcodeado en la config de una VM y el bus cambia, la VM va a intentar pasar un dispositivo que en esa posición ya no es tu GPU (o directamente no existe), y va a fallar de forma poco clara.

El **Resource Mapping** (Datacenter → PCI Devices → Add) le pone un nombre lógico a la tarjeta (identificada internamente por su Vendor/Device ID, no solo por el bus) y Proxmox se encarga de resolver la ubicación real en cada arranque. La VM referencia el nombre (`mapping=Tesla_P4`), no el bus.

```bash
qm config <ID_VM> | grep hostpci
```

```
hostpci0: mapping=Tesla_P4,pcie=1
```

En vez de:

```
hostpci0: 0000:05:00.0,pcie=1
```

### Cómo confirmar que el mapping sigue siendo válido

La interfaz de Proxmox (Datacenter → Resource Mappings) muestra un estado por cada mapping — **"Mapping matches host data"** en verde significa que la ubicación registrada coincide con lo que el sistema detecta ahora mismo. Si alguna vez ves un estado distinto ahí (por ejemplo, tras cambios de hardware o BIOS), es la primera señal de que hay que revisar el mapping antes de intentar arrancar la VM.

---

## 7. Resumen del capítulo — checklist de validación

Antes de pasar a crear la VM, confirmá estos cuatro puntos con evidencia (no de memoria):

```bash
# 1. IOMMU activo a nivel hardware
dmesg | grep -e DMAR -e IOMMU | head -5

# 2. Cmdline correcto
cat /proc/cmdline

# 3. GPU tomada por vfio-pci, no por nouveau/nvidia
lspci -k -s <bus_de_tu_gpu>

# 4. IOMMU group de la GPU, aislado o no
readlink /sys/bus/pci/devices/0000:<bus_de_tu_gpu>/iommu_group
```

Si los cuatro dan el resultado esperado, el host está listo. El siguiente informe cubre los incidentes que pueden aparecer durante este proceso (efecto cascada sobre otros servicios, diagnóstico sin IPMI) y la creación correcta de la VM.

---

**Siguiente:** [[02-Passthrough-TeslaP4-Diagnostico-Incidentes-VM]] **Anterior:** [[00-Passthrough-TeslaP4-Proxmox-Indice]]