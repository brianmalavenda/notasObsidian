tipo::
tags:: #virus #red #seguridad 
fecha:: 2026-08-27
links::
links_externos::

Desde la perspectiva forense, la gran mayoría del malware (99%) vive en el sistema de archivos o en la memoria RAM. Un simple formateo del disco lo elimina. Sin embargo, existe el **1% de malware de nivel superior (Rootkits de hardware/firmware)** que **SOBREVIVE a un formateo de disco**.

### ¿Qué tipos de virus sobreviven a un formateo convencional?

1. **Bootkits / UEFI Rootkits (Ej. LoJax):** No infectan Windows ni Linux. Infectan la placa base (la BIOS/UEFI). Cada vez que enciendes la PC, el código malicioso se ejecuta _antes_ de que arranque el sistema operativo, inyectándose directamente en el kernel. Formatear el M.2 no sirve de nada porque el malware reside en el chip SPI flash de la motherboard. Ejemplos reales documentados: LoJax, MoonBounce, CosmicStrand — todos sobreviven no solo a formateo sino a **reemplazo completo del disco**.

2. **Malware de Firmware de Discos (HDD/SSD Rootkits):** El malware (famoso el de la ecuación _Equation Group_) reescribe el firmware (controlador interno) del propio disco duro o SSD. Cuando el sistema operativo le pide al disco que se formatee, el disco _le miente_ al sistema operativo. Finge que borra todo, pero mantiene el malware escondido en una partición oculta a nivel de hardware. Documentado por el grupo Equation (asociado a herramientas de la NSA filtradas) — reprogramaban firmware de fabricantes como Seagate, Western Digital, Toshiba.

3. **BadUSB / Firmware de Periféricos:** Tarjetas de red, controladoras RAID, o cualquier periférico con su propia ROM programable puede alojar malware que se ejecuta antes de que el sistema operativo cargue, independiente del disco. El malware reprograma el chip del controlador USB, de la tarjeta de red (NIC) o de la webcam. Formatear la PC no cambia el firmware de esos dispositivos.

4. **Intel ME / AMT comprometido**  
	El Management Engine corre en un procesador separado dentro del chipset, con acceso a memoria y red independiente del sistema operativo. Es una superficie de ataque real y documentada (vulnerabilidades CVE conocidas en ME).
5. **Compromiso del BMC/IPMI**  
	Muy relevante para vos porque estás evaluando servidores con gestión remota (IPMI mencionado en las placas que buscaste): el BMC es esencialmente una computadora aparte dentro del servidor, con su propio firmware, su propia memoria, a veces su propio almacenamiento — corre incluso con el servidor apagado. Un BMC comprometido sobrevive a cualquier acción sobre el sistema operativo principal.

6. **Implantes de Hardware (Hardware Implants):** Algo físicamente instalado en el bus PCIe o en los puertos SATA (ej. un keylogger de hardware o un chip espía). Esto requiere extracción física.
7. **Conexión por Bluetooth para el rastreo:** Algunas notebooks modernas (como las que integran tecnologías de rastreo nativas estilo _Tile_ o la red _Buscar_ de Apple) pueden ser localizadas de forma pasiva **si tienen el Bluetooth encendido**, incluso sin internet. La notebook emite una señal Bluetooth cifrada. Si un teléfono celular de otra persona (que sí tiene internet y GPS) pasa cerca, detecta esa señal de forma anónima y reporta la ubicación de la notebook a la nube.


---
#### Referencias

