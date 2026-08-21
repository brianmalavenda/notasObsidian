
tipo::
tags:: [proxmox] , [gpu-passthrough], [nvidia], [tesla-p4]
fecha:: 2026-07-23
links::
links_externos::

> Esta es la parte más larga porque fue, en la práctica, donde más tiempo se fue: no por un solo problema grande, sino por varios problemas chicos y de naturaleza completamente distinta apilados uno atrás del otro (licenciamiento, un driver que toma la tarjeta antes de tiempo, espacio en disco, y finalmente Secure Boot). Cada uno con síntomas parecidos en la superficie ("la instalación del driver falla") pero causas totalmente distintas — por eso el orden de diagnóstico importa tanto como la solución en sí.
## Contexto de referencia

- VM: Debian 13 (Trixie), kernel `6.12.95+deb13-amd64`
- GPU pasada: Tesla P4 (GP104GL, arquitectura Pascal, uso datacenter/compute)
- Driver instalado: NVIDIA Data Center Driver, rama R580 (`580.95.05`)

---

## 14. Licenciamiento: por qué el passthrough completo NO requiere suscripción NVIDIA

### La confusión habitual

Es común encontrar en foros advertencias de que usar una Tesla (P4, P40, P100, etc.) "requiere licencia y pago mensual a NVIDIA". Esto genera dudas legítimas al planear un proyecto sin presupuesto para licenciamiento — pero la advertencia, tal como suele citarse, mezcla dos escenarios de uso completamente distintos de esta misma familia de tarjetas.

### Los dos escenarios, y por qué solo uno paga licencia

- **NVIDIA vGPU (paga):** consiste en **particionar** una sola GPU física en varias GPUs virtuales, para repartirla simultáneamente entre múltiples VMs (por ejemplo, cuatro VMs usando una fracción de la misma Tesla cada una). Esto se implementa vía `mdev` (mediated devices) y sí requiere una suscripción a NVIDIA AI Enterprise / vGPU software, con un servidor de licencias corriendo en la red. Es el modelo de negocio pensado para proveedores de VDI/cloud que necesitan multiplexar una tarjeta cara entre muchos clientes.
- **Passthrough completo (el usado en esta guía):** la tarjeta se entrega **entera** a **una sola VM**, exactamente como la vería un sistema physical/bare-metal. En este modo se usa el **driver estándar de NVIDIA para Linux datacenter**, que es gratuito, sin suscripción ni servidor de licencias — es el mismo tipo de instalación que harías en un servidor físico con la tarjeta enchufada directamente.

### Cómo confirmar en qué escenario estás

La clave está en la configuración `hostpci0` de la VM: si aparece como device completo (`mapping=Tesla_P4`, sin sufijos de tipo `mdev`), estás en modo passthrough completo. El modelo `mdev`/vGPU se configura de forma explícitamente distinta, seleccionando un "MDev Type" al agregar el PCI device — una opción que, para este caso, se dejó sin usar.

`nvidia-smi`, y las capacidades de NVENC/NVDEC para transcoding y CUDA para cómputo general, funcionan sin restricciones ni pago en el modo de passthrough completo.

---

## 15. Elegir la rama de driver correcta para una GPU Pascal datacenter

### Por qué esto no es trivial

NVIDIA descontinúa soporte de arquitecturas de GPU más viejas en sus ramas de driver más nuevas, y esto sucede en momentos distintos según la línea de producto:

- Las ramas de driver **GeForce/Game Ready** (consumer) discontinuaron soporte para Pascal en versiones recientes (la serie 590, por ejemplo).
- Las ramas de **Data Center Driver** siguen un ciclo de vida distinto y más largo, pensado para hardware que permanece en producción durante años en datacenters.

Al momento de esta instalación, la rama **R580** del Data Center Driver todavía incluye soporte oficial para Pascal (incluida la Tesla P4, GP104). Esto puede cambiar con el tiempo — vale la pena, antes de descargar, confirmar en la matriz de soporte oficial de NVIDIA que la tarjeta específica sigue listada para la rama que estás por instalar, en vez de asumir que "la versión más nueva siempre sirve".

### Dónde descargar

El driver correcto se obtiene desde el buscador oficial (`nvidia.com/Download`), filtrando específicamente por "Tesla P4" y "Linux 64-bit" — no por "GeForce", que te va a llevar a la rama consumer equivocada, y no vas a encontrar el `.run` correcto buscando genéricamente "NVIDIA driver Debian".

```bash
cd /root/nvidia-install   # ver punto 17 sobre por qué no usar /tmp
wget https://us.download.nvidia.com/tesla/580.95.05/NVIDIA-Linux-x86_64-580.95.05.run
```

---

## 16. Conflicto con `nouveau` dentro de la VM guest

### Por qué aparece, aunque ya se resolvió en el host

Este es un punto que suele confundir porque **ya se resolvió un problema idéntico en el Informe 1** (punto 4), pero en una capa distinta del sistema. El blacklist de `nouveau` que se configuró ahí fue **en el host Proxmox**, para permitir que `vfio-pci` tomara la tarjeta y pudiera pasarla a la VM.

Pero dentro de la VM guest, es un sistema operativo completamente nuevo (Debian recién instalado) — con su propio kernel, y su propia decisión automática de qué driver cargar para el hardware que detecta. Al arrancar por primera vez y ver una GPU NVIDIA en el bus PCI (ahora expuesta dentro de la VM gracias al passthrough), Debian intenta cargar `nouveau` ahí también, por la misma razón que lo hacía el host: es el driver open-source incluido por defecto.

### El síntoma

Al correr el instalador `.run` de NVIDIA:

```
WARNING: The Nouveau kernel driver is currently in use by your system.
This driver is incompatible with the NVIDIA driver, and must be disabled before proceeding.

ERROR: Installation has failed.
```

### La solución (idéntica en estructura al Informe 1, aplicada ahora en el guest)

```bash
echo "blacklist nouveau
options nouveau modeset=0" > /etc/modprobe.d/blacklist-nouveau.conf

update-initramfs -u
reboot
```

Verificar después del reboot:

```bash
lsmod | grep nouveau
```

No debería devolver nada.

---

## 17. El problema real detrás de "Failed CC sanity check" y "No space left on device"

Este fue el punto más engañoso del proceso completo, porque el primer mensaje de error que aparece **no es la causa real**.

### El primer síntoma, y por qué no hay que tomarlo literal

```
The C compiler 'x86_64-linux-gnu-gcc-14' does not appear to be able to
create object files.
*** Failed CC sanity check. Bailing out! ***
```

La reacción natural es sospechar del compilador: reinstalar `build-essential`, verificar versiones de `gcc` contra los headers del kernel, confirmar que `cc1` existe. Todo esto vale la pena descartar (y en este caso se descartó, compilando manualmente un `.c` de prueba con éxito), pero **el compilador nunca fue el problema real**.

### La causa real, escondida más abajo en el log

Reintentando la instalación, el log completo (no solo el resumen final) mostró:

```
ld: final link failed: No space left on device
```

Esto aparece **después** de que decenas de archivos `.o` se compilaron correctamente — el compilador funcionaba perfectamente, era el paso de _linkeo final_ el que se quedaba sin espacio para escribir el binario resultante.

### Por qué "sin espacio" no era evidente con `df -h`

Acá está el detalle técnico que explica la confusión: un `df -h` general puede mostrar el disco principal con espacio de sobra (en este caso, 26GB libres de 32GB en `/`), y aun así fallar por falta de espacio. La razón:

```
tmpfs   2,0G   379M   1,6G   20%   /tmp
```

**`/tmp` en Debian está montado como `tmpfs`** — un filesystem que vive en **RAM**, no en el disco. Tiene un tamaño fijo asignado (2GB por defecto en este caso), completamente independiente del espacio disponible en el disco real. El instalador de NVIDIA extrae el paquete y compila el módulo del kernel dentro de `/tmp` por defecto, generando bastante volumen de archivos objeto intermedios — y con GPUs modernas con components complejos (como los del subsistema UVM de la Tesla P4), ese volumen puede superar cómodamente 2GB durante la compilación, sin que el disco principal se entere en absoluto.

### La solución, y por qué hay que aplicarla dos veces (persistencia)

Agrandar el tmpfs en caliente:

```bash
mount -o remount,size=8G /tmp
df -h /tmp
```

**Detalle importante:** este cambio **no persiste a través de reinicios de la VM**. Si en algún momento posterior del proceso hace falta reiniciar la VM por otro motivo (como pasó acá, para aplicar el cambio de Secure Boot del punto 19), `/tmp` vuelve a su tamaño default y el problema de espacio puede reaparecer sin ningún cambio aparente en la causa — lo cual es confuso si no se tiene presente que es un límite de RAM montada, no de disco.

Para hacerlo persistente:

```bash
echo "tmpfs /tmp tmpfs defaults,size=8G 0 0" >> /etc/fstab
```

Y antes de cada reintento, limpiar los restos de intentos fallidos anteriores (el instalador de NVIDIA no borra sus carpetas temporales si falla a mitad de camino):

```bash
find /tmp -maxdepth 1 -type d -name "makeself.*" -exec rm -rf {} \;
```

---

## 18. Cómo leer el log de instalación de NVIDIA sin perderse en el ruido

El instalador `.run` de NVIDIA es verborrágico y mezcla, en la misma pantalla, errores reales con warnings completamente irrelevantes para un servidor headless. Aprender a separarlos ahorra tiempo de diagnóstico:

### Warnings esperables y seguros de ignorar en un servidor sin entorno gráfico

- `nvidia-installer was forced to guess the X library path` — no hay X11 instalado, es normal y no afecta NVENC/CUDA.
- `Unable to find a suitable destination to install 32-bit compatibility libraries` — no hace falta soporte de 32 bits para transcoding.
- `This NVIDIA driver package includes Vulkan components, but no Vulkan ICD loader was detected` — Vulkan no se usa para transcoding de video ni para Jellyfin.

### Dónde mirar cuando algo sí falla

El resumen final que muestra la terminal (`ERROR: Installation has failed...`) rara vez tiene el detalle suficiente. El log completo sí:

```bash
cat /var/log/nvidia-installer.log | tail -100
```

Y para ir directo al grano en un fallo de compilación específico, filtrar por la etapa que interesa:

```bash
grep -A 20 "cc_sanity_check" /var/log/nvidia-installer.log
```

La lección general: **cuando un instalador falla, el mensaje final en pantalla es un resumen — la causa real casi siempre está unas cuantas líneas más arriba, en el log completo.**

---

## 19. Secure Boot bloqueando la carga del módulo compilado

### El síntoma, después de resolver el espacio en disco

Con `/tmp` ya con espacio suficiente, la compilación del módulo `nvidia.ko` terminó exitosamente por primera vez — pero el instalador igual reportó fallo, en una etapa distinta:

```
Kernel module compilation complete.
-> The target kernel has CONFIG_MODULE_SIG set, which means that it supports
   cryptographic signatures on kernel modules.
ERROR: The kernel module failed to load. Secure Boot is enabled on this
       system, so this is likely because it was not signed by a key that
       is trusted by the kernel.
...
Loading of unsigned module is rejected
```

### El concepto: qué es Secure Boot y por qué afecta a un módulo recién compilado

Secure Boot es una funcionalidad de UEFI que verifica firmas criptográficas en cada componente de la cadena de arranque — incluyendo, en kernels Linux modernos con `CONFIG_MODULE_SIG` activado, los **módulos del kernel** que se cargan dinámicamente después del boot (como `nvidia.ko`).

Un módulo compilado localmente con DKMS, como el de NVIDIA, **no viene firmado por ninguna clave que el sistema reconozca como confiable** (a menos que se configure explícitamente un proceso de firma con una clave propia, lo cual está fuera del alcance de esta guía por ser innecesariamente complejo para un homelab). Con Secure Boot activo, el kernel simplemente **rechaza cargar** ese módulo sin firma — el `.ko` existe en disco, está compilado correctamente, pero el kernel se niega a insertarlo en memoria.

### Por qué esto sorprende en una VM (a diferencia de un sistema físico)

En un sistema físico, Secure Boot se gestiona una sola vez desde el BIOS/UEFI real de la placa. En una VM con firmware OVMF, Proxmox emula un firmware UEFI completo **por VM**, con su propio estado de Secure Boot — que puede estar activo por defecto según cómo se creó el `efidisk0`, independientemente de la configuración del hardware físico subyacente.

### La solución más simple: desactivarlo en el firmware OVMF de esa VM puntual

No hace falta (ni conviene) tocar Secure Boot a nivel del host físico — el cambio se hace específicamente en el firmware virtual de la VM que va a correr el driver sin firmar.

**Opción rápida vía configuración**, recreando el disco EFI sin claves preinstaladas:

```bash
qm stop <ID_VM>
qm set <ID_VM> -efidisk0 local-lvm:vm-<ID_VM>-disk-0,efitype=4m,pre-enrolled-keys=0,size=4M
```

En la práctica, este cambio de configuración por sí solo puede no ser suficiente para desactivar Secure Boot si el firmware ya había inicializado su estado — en ese caso hace falta el paso manual del punto siguiente.

---

## 20. Navegar el menú de firmware OVMF sin confundirlo con GRUB

Este paso generó una confusión frecuente en la práctica que vale la pena anticipar: **hay dos menús distintos que aparecen en secuencia al bootear una VM con OVMF**, y hay que entrar al primero, no al segundo.

### La secuencia de arranque, y dónde hay que interceptarla

```
Firmware OVMF (TianoCore)  ← acá hay que entrar, apretando Esc temprano
   ↓ (si no se intercepta, continúa automáticamente)
GRUB (bootloader del sistema operativo instalado en el disco)
   ↓
Kernel de Debian
```

Si se aprieta `Esc` tarde, es común terminar en la **consola de rescate de GRUB** (`grub_es>` o `grub>`) en vez del menú de firmware — son cosas completamente distintas: GRUB ya es una etapa posterior, del sistema operativo, y desde ahí no se puede acceder a la configuración de Secure Boot del firmware. Si esto pasa, escribir `normal` en el prompt de GRUB retoma el arranque normal, y hay que reiniciar la VM para reintentar con mejor timing.

### Cómo entrar correctamente

1. Reiniciar la VM y tener la consola de Proxmox ya abierta _antes_ de arrancarla (Start desde ahí mismo), para no perderse el primer frame.
2. Apenas aparece cualquier texto en pantalla — el logo/pantalla de selección de idioma de TianoCore, con opciones como "Device Manager", "Boot Manager", "Continue" — ya se está en el lugar correcto. No hace falta ni apretar Esc si se llega a esta pantalla directamente con el Start; es la pantalla de configuración de firmware en sí.

### Dentro del menú: dónde está la opción de Secure Boot

```
Device Manager
  → Secure Boot Configuration
    → Attempt Secure Boot   [X]  ← desmarcar (Enter o Espacio sobre el checkbox)
```

Guardar y salir (normalmente con una opción tipo "Commit Changes and Exit" en esa misma pantalla, o simplemente saliendo con `Esc` hasta el menú principal y seleccionando **Reset**).

### Verificación

```bash
mokutil --sb-state
```

Tiene que devolver `SecureBoot disabled`.

**Atención a un error de contexto fácil de cometer:** este comando hay que correrlo **dentro de la VM guest**, no en el host Proxmox. Ambos tienen su propio estado de Secure Boot completamente independiente — correrlo en el lugar equivocado da un resultado que parece válido pero no tiene relación con el problema que se está diagnosticando.

---

## 21. Verificación final: `nvidia-smi` dentro de la VM

Con Secure Boot desactivado y `/tmp` con espacio suficiente, se reinstala el driver (el módulo ya había compilado bien en el intento anterior; ahora además puede cargar):

```bash
cd /root/nvidia-install
./NVIDIA-Linux-x86_64-580.95.05.run --dkms --no-x-check --silent
```

Verificación:

```bash
lsmod | grep nvidia
nvidia-smi
```

Salida esperada — confirma driver cargado, tarjeta reconocida, y disponibilidad de memoria de video:

```
NVIDIA-SMI 580.95.05    Driver Version: 580.95.05    CUDA Version: 13.0

GPU  Name        Persistence-M  Bus-Id        Disp.A  Volatile Uncorr. ECC
 0   Tesla P4    Off            00000000:01:00.0 Off   0
    21W / 75W                   0MiB / 7680MiB
```

Con este resultado, el passthrough y el driver quedan completamente operativos dentro de la VM — punto de partida para instalar Docker, el NVIDIA Container Toolkit, y configurar NVENC en Jellyfin (contenido a documentar en una próxima entrada, una vez validado en producción).

---

## Resumen del capítulo

|Síntoma superficial|Causa real|Dónde se resuelve|
|---|---|---|
|"Nouveau incompatible"|Driver open-source tomó la tarjeta dentro del guest|Blacklist de nouveau en la VM|
|"Failed CC sanity check"|Engañoso — no es el compilador|Ver log completo, no el resumen|
|"No space left on device" (en el link final)|`/tmp` es tmpfs de 2GB en RAM, no el disco|Agrandar tmpfs, hacerlo persistente en fstab|
|"Unsigned module rejected"|Secure Boot activo en el firmware OVMF de la VM|Desactivar Secure Boot en Device Manager del firmware, dentro de la VM guest|

La lección transversal a todo este informe: **el mensaje de error inicial rara vez es la causa raíz**. Vale la pena, sistemáticamente, ir al log completo antes de actuar sobre el primer síntoma visible.

---

**Anterior:** [[02-Passthrough-TeslaP4-Diagnostico-Incidentes-VM]] **Índice:** [[00-Passthrough-TeslaP4-Proxmox-Indice]]