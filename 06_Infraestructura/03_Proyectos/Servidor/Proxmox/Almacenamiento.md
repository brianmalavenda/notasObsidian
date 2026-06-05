tipo::
tags::
fecha:: 2026-06-02
links::
links_externos::


En el nodo principal de la instalación de proxmox se encuentra en la parte de Disks, todas las unidades de almacenamiento 

![[Pasted image 20260602075404.png]]

En este caso, la primer unidad es la del disco de instalación que es una memoria tipo NVME de 1TB, donde esta particionada de tal manera que una parte queda reservada para la BIOS, otra para el EFI y otra queda de almacenamiento puro.

Los otros dos discos son discos mecanicos HDD donde tiene creada una partición ZFS del mismo tamaño del disco porque ...

Debajo de la opción de menú DISK que estamos viendo hay una subopcion llamada LVM. Allí se encuentra el volumen virtualizado del disco NVME. 

![[Pasted image 20260602075703.png]]

Se ve que el espacio asignado al volumen es casi el total del disco salvo las otras particiones que vimos que no estan destinadas a datos de almacenamiento de usuario.
LVM-Thin, la opción que esta debajo es la que indica cuanto espacio del volumen de almacenamiento se viene usando por parte del usuario.

![[Pasted image 20260602075842.png]]

Las opciones "Directoy" y "ZFS" quedan para describirse después.

En el Datacenter vemos como esas unidades de memoria física tienen una división lógica administradas por Proxmox. **local** es la unidad de almacenamiento para backups, imágenes de SO, templates, etc y **local-lvm** es la carpeta que se corresponde con la unidad de almacenamiento anterior que vimos que es el volumen de datos del disco NVME de 1 TB. 

![[Pasted image 20260602080108.png]]

En el caso de **vm-storage** es una unidad lógica de almacenamiento creada por el administrador (yo) donde se comparte con mi NAS (TrueNAS). El contenido es del mismo tipo que "**local**", pero con la diferencia de ser una unidad compartida NFS. 
Si vamos a la opción de abajo del menú principal, **Backups** allí como posibles unidades lógicas donde guardar los/el backup aparecen local y vm-storage por el tipo de contenido que se les asignó al momento de crearlas.

![[Pasted image 20260602080707.png]]


Que son estos archivos?
![[Pasted image 20260602081206.png]]

![[Pasted image 20260602081525.png]]


---
#### Referencias

