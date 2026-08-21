tipo::
tags:: [proxmox], [gpu-passthrough], [nvidia], [tesla-p4]
fecha:: 2026-07-23
links::
links_externos::

> Esta segunda parte cubre dos cosas que en teoría no están relacionadas, pero que en la práctica suelen aparecer juntas: los sustos que da el hipervisor justo después de tocar la configuración de arranque (IOMMU, GRUB), y la creación correcta de la VM que va a recibir la GPU. El objetivo de la primera sección no es solo resolver el susto puntual, sino dejar un método para no entrar en pánico la próxima vez que un cambio de bajo nivel parezca haber roto todo el homelab.

---
## 8. Shell que no responde: diferenciar un fallo real del host de un simple timeout de proxy

### El síntoma

Después de reiniciar el host tras cambios de GRUB/IOMMU, es común intentar entrar por la Shell web de Proxmox (Datacenter → Nodo → Shell) y encontrarse con errores como:

```
error reading from pty: Input/output error (os error 5)
```

o, si el prompt de login sí aparece:

```
login: timed out after 60 seconds
```

### Por qué da miedo, y por qué en la mayoría de los casos no debería

Es tentador leer esto como "el sistema no arrancó bien" — pero hay que separar dos capas que son técnicamente independientes:

1. **El sistema operativo del host**, que arrancó (o no) de forma completamente autónoma, sin importar si alguien lo está mirando por la interfaz web.
2. **`termproxy`**, el proceso que traduce esa terminal a texto que se puede ver dentro del navegador. Es un componente de la capa de gestión de Proxmox, no del sistema base.

Un timeout de 60 segundos en el prompt de login, por ejemplo, casi siempre es simplemente que la sesión de proxy tiene un límite de inactividad corto — si abrís la shell y no escribís usuario/contraseña lo suficientemente rápido, la corta. **Eso confirma, paradójicamente, que el sistema arrancó bien**: llegó hasta el prompt de login, que es de las últimas etapas del boot.

### Cómo confirmarlo sin necesidad de tocar nada físico

Antes de asumir lo peor, probá acceso por una vía que no dependa del proxy de la interfaz web:

```bash
ssh root@<IP_del_host>
```

- **Si SSH funciona:** el host está sano, el problema es puntual de la sesión de shell del navegador. Reintentá la shell web escribiendo rápido, o quedate trabajando por SSH, que no tiene ese timeout tan agresivo.
- **Si SSH también falla o no conecta:** ahí sí hay que sospechar de un problema real de arranque, y pasa a aplicar el punto 10 (diagnóstico sin IPMI).

Este orden de verificación (SSH antes que pánico) ahorra bastante tiempo, porque la mayoría de los sustos de shell web resultan ser justamente eso: un timeout de proxy, no una falla del sistema.

---

## 9. El efecto cascada: por qué un cambio de bajo nivel puede tumbar cosas sin relación aparente

### El síntoma

Tras activar IOMMU y reiniciar, pueden aparecer errores en servicios que en apariencia no tienen nada que ver con GPU ni con passthrough:

- Un LXC que no arranca: `failed to connect to monitor socket: Connection refused`
- Una VM (por ejemplo TrueNAS) que no arranca: `org.freedesktop.DBus.Error.NoReply`
- Otra VM que falla con: `storage 'xxx' is not online`

### Por qué pasa esto: entender la dependencia, no solo el síntoma

La clave para no perder tiempo tratando cada error como un problema aislado es reconocer **cadenas de dependencia** dentro de la infraestructura. Un ejemplo típico en un homelab con almacenamiento centralizado:

```
Host reinicia con nueva config de IOMMU
   ↓
Una VM de storage (ej. TrueNAS) tiene su HBA/controladora pasada por PCI passthrough
   ↓
Activar IOMMU renumera los buses PCI del sistema
   ↓
La config de esa VM sigue apuntando al bus PCI viejo (si no usa Resource Mapping)
   ↓
TrueNAS no puede arrancar (timeout de DBus esperando el hardware que ya no está ahí)
   ↓
El storage que TrueNAS exportaba (NFS/iSCSI) queda offline
   ↓
Cualquier VM o LXC con su disco en ese storage falla al arrancar
```

Un solo cambio en el eslabón más bajo de la cadena (renumeración de bus) se propaga hacia arriba y se manifiesta como tres errores aparentemente sin relación. Esto es exactamente el mismo mecanismo que resolvimos con el Resource Mapping en el Informe 1 (punto 6) — la diferencia es que acá el dispositivo afectado no es la GPU, sino la controladora de almacenamiento de otra VM que **no** estaba usando mapping.

### Cómo diagnosticar sin perder tiempo

1. **Identificá cuál es la pieza "raíz"** de la infraestructura (en un homelab con NAS centralizado, casi siempre es el storage). Empezá el diagnóstico ahí, no por el síntoma que viste primero.
2. Revisá la config de esa VM buscando referencias a hardware pasado por bus crudo:
    
    ```bash
    qm config <ID_VM_STORAGE>
    ```
    
    Buscá líneas `hostpci0:` y comparalas contra la ubicación **actual** del hardware:
    
    ```bash
    lspci -nn | grep -i -E "sata|raid|sas|ahci|lsi|broadcom"
    ```
    
3. Si el bus no coincide, ahí está la causa raíz. Corregir esa VM (actualizando el bus, o mejor aún, migrando a un Resource Mapping) suele destrabar en cadena todo lo que dependía de ella.

### La lección de fondo

Este incidente es también el mejor argumento práctico para el punto 6 del Informe 1: **cualquier dispositivo pasado por PCI passthrough en el homelab —no solo la GPU que estás instalando ahora— debería idealmente usar Resource Mapping**, precisamente porque este tipo de renumeración de bus no es exclusiva de cuando activás IOMMU por primera vez; puede repetirse con futuras actualizaciones de BIOS o cambios de hardware.

---

## 10. Diagnóstico de arranque sin IPMI/BMC

### La limitación de hardware a tener en cuenta

Las placas de consumo o prosumer, como la ASRock X99 Taichi, no incluyen un chip BMC (Baseboard Management Controller) con IPMI/iKVM, que es lo que en servidores dedicados permite ver la consola del sistema remotamente incluso si el sistema operativo no arrancó. Esto significa que, si el host queda en un estado donde ni SSH ni la shell web responden, **la única forma de ver qué está pasando es acceso físico**.

### Qué preparar de antemano (antes de que haga falta)

Vale la pena, antes de hacer cambios riesgosos como los de IOMMU, tener a mano:

- Un monitor y teclado que puedan conectarse rápido al server.
- Saber que, en un setup con doble GPU como este, la salida de video del host generalmente sale por la tarjeta que se dejó _fuera_ del aislamiento (en este caso, la GT 1030) — confirmá esto de antemano si tenés dudas, no en el momento de la emergencia.

### Qué mirar en la consola física

Al conectar el monitor, la pregunta clave es: **¿el sistema llegó al prompt de login, o se quedó colgado antes?**

- **Si llegó al prompt de login:** el sistema arrancó correctamente a nivel de kernel y servicios base. Cualquier problema restante es de un servicio específico (como los del punto 9), no del arranque en sí. Podés loguearte directo ahí y diagnosticar con `journalctl`.
- **Si se colgó antes,** el mensaje en pantalla en el punto exacto del cuelgue es la pista más valiosa — anotalo o sacale una foto antes de reiniciar, porque se pierde.

### Comandos útiles una vez con acceso a una shell (física o SSH)

```bash
# Ver los eventos de systemd más recientes
journalctl -b | tail -200

# Filtrar específicamente por servicios de gestión de Proxmox
journalctl -u pvedaemon -u qmeventd --since "10 minutes ago" | grep -i -E "error|fail"
```

---

## 11. Parámetros correctos de VM para passthrough: por qué `q35` + `OVMF`

Con el host ya diagnosticado y sano, se puede pasar a crear la VM. Acá los tres parámetros que importan y por qué:

- **Machine type `q35`:** emula un chipset más moderno (con soporte nativo de PCI Express) en comparación con el chipset `i440fx` (el otro tipo disponible en Proxmox, que emula PCI legacy). Una GPU pasada como dispositivo PCIe necesita que la VM tenga topología PCIe real para comunicarse correctamente con el driver del guest — con `i440fx` es posible que funcione a medias o directamente falle.
- **BIOS `OVMF` (UEFI) en vez de SeaBIOS:** SeaBIOS emula un BIOS legacy tradicional; OVMF emula firmware UEFI. Los drivers GPU modernos (incluido el de NVIDIA para Linux) están pensados para arrancar en sistemas UEFI, y algunos comportamientos de inicialización de la tarjeta dependen de esto.
- **PCI-Express marcado al agregar el dispositivo:** le indica a QEMU que exponga el dispositivo dentro de la topología PCIe virtual de la VM, coherente con el `q35` de arriba, en vez de como PCI legacy.

Al usar `OVMF`, Proxmox obliga a agregar un **EFI Disk** — un disco pequeño (4MB) donde se guarda el estado de las variables NVRAM de UEFI (configuración de boot, y como se ve en el informe 3, las claves de Secure Boot). No es opcional, sin él la VM no puede arrancar con OVMF.

```
Machine: q35
BIOS: OVMF (UEFI)
EFI Disk: se agrega automáticamente al elegir OVMF
```

Para el resto de los recursos (disco, CPU, RAM, red), los valores dependen del uso que le vayas a dar a la VM — en este caso, orientado a correr Jellyfin, no hay requerimientos especiales más allá de recursos moderados.

---

## 12. Por qué un LXC no sirve para este enfoque (y cuándo sí serviría)

Este punto vale la pena documentarlo porque es un error fácil de cometer, sobre todo viniendo de un uso intensivo de LXC para el resto de la infraestructura (como es el caso en este homelab).

### La diferencia de fondo

- **Un contenedor LXC comparte el kernel del host.** No tiene su propio kernel ni sus propios drivers de dispositivo — usa los que ya están cargados en el hipervisor. El acceso a la GPU se logra dándole al contenedor visibilidad sobre los device nodes que el **driver del host** ya expone (`/dev/nvidia0`, `/dev/nvidiactl`, etc.), vía bind mount.
- **Una VM con PCI passthrough tiene su propio kernel completo**, y el dispositivo se le entrega de forma exclusiva y aislada — el driver NVIDIA corre _dentro_ de la VM, no en el host.

### Por qué esto importa para la decisión

Estas dos aproximaciones son **mutuamente excluyentes** para la misma tarjeta: si el objetivo es passthrough (que fue la decisión tomada en este caso, por mayor aislamiento y no depender de que las versiones de driver del host y del contenedor coincidan), la GPU necesita estar **bloqueada del driver del host** (blacklist + vfio-pci, como en el Informe 1, punto 4). Eso es exactamente lo opuesto de lo que necesita un LXC, que requiere que el **host** tenga el driver NVIDIA cargado y funcionando (`nvidia-smi` corriendo en el propio Proxmox), para poder exponer esos device nodes al contenedor.

Si en algún punto se crea un LXC apuntando a `/dev/nvidia*` mientras la tarjeta está aislada con vfio-pci para passthrough, el LXC simplemente no va a encontrar esos archivos de dispositivo, porque nunca se crearon (el driver del host nunca cargó).

### Cuándo sí conviene LXC en vez de VM

Si el objetivo fuera correr **múltiples** servicios livianos accediendo a la misma GPU desde el propio host (sin necesidad de aislamiento fuerte entre ellos, y aceptando que todos dependen de la misma versión de driver instalada en Proxmox), LXC puede ser más eficiente en recursos que levantar una VM completa. Es una decisión de trade-off entre aislamiento (VM) y eficiencia/simplicidad compartida (LXC), no una elección técnica "correcta" universal.

---

## 13. Agregar el dispositivo PCI a la VM correctamente

Con la VM creada (`q35` + `OVMF`), el paso final de esta sección es asociarle la GPU.

### Por Resource Mapping, no por bus crudo

Vía interfaz: **VM → Hardware → Add → PCI Device**

- **Device:** elegir **"Mapped Device"** y seleccionar el mapping creado en el Informe 1 (punto 6) — no "Raw Device". Esto es lo que hace que la VM sobreviva a futuras renumeraciones de bus sin necesidad de editar su configuración a mano.
- ☑ **All Functions:** relevante cuando la tarjeta expone más de una función PCI (por ejemplo, una GPU con salida de audio HDMI integrada tiene una función de video y otra de audio, y conviene pasar ambas juntas). Para la Tesla P4 específicamente esto es indistinto, porque al ser una GPU orientada a cómputo no expone función de audio — tiene una sola función.
- ☑ **PCI-Express:** coherente con la elección de `q35` del punto 11.
- ☐ **Primary GPU:** se deja sin marcar cuando la tarjeta no tiene salida de video propia (como la P4, pensada para cómputo/transcoding, no para desplegar imagen). Esta opción es para cuando la VM va a usar la GPU pasada como su única salida de video — no es el caso acá.

### Verificar que quedó bien, y el detalle del flag `pcie=1`

```bash
qm config <ID_VM> | grep hostpci
```

El resultado esperado:

```
hostpci0: mapping=Tesla_P4,pcie=1
```

Si falta el `,pcie=1` al final (puede pasar si el checkbox de PCI-Express no llegó a tildarse en la interfaz), agregalo manualmente:

```bash
qm set <ID_VM> -hostpci0 mapping=Tesla_P4,pcie=1
```

Sin este flag, QEMU expone el dispositivo en modo PCI legacy dentro de una VM configurada como `q35` — una inconsistencia de topología que puede generar comportamiento errático del driver dentro del guest, incluso si a simple vista la GPU aparece detectada.

---

## Resumen del capítulo

- Ante un fallo de shell web, probar SSH antes de asumir que el host está caído.
- Ante fallos en cascada de servicios sin relación aparente tras un cambio de bajo nivel, buscar la pieza "raíz" de la cadena de dependencias (típicamente storage) en vez de tratar cada síntoma por separado.
- Sin IPMI, tener preparado el acceso físico de antemano, no en el momento de la emergencia.
- La VM para passthrough necesita `q35` + `OVMF`, con el dispositivo PCI agregado vía Mapped Device y `pcie=1`.
- LXC y passthrough completo son enfoques incompatibles para la misma tarjeta — la elección se hace una sola vez, al principio.

El siguiente informe, el más largo de los tres, cubre todo lo que pasó _dentro_ de la VM al instalar el driver de NVIDIA: licenciamiento, conflictos con `nouveau`, espacio en disco, y el bloqueo de Secure Boot.

---

**Siguiente:** [[03-Passthrough-TeslaP4-Driver-SecureBoot-Jellyfin]] **Anterior:** [[01-Passthrough-TeslaP4-Preparacion-Host]]