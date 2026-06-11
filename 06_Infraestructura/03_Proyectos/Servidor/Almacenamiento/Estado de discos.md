tipo::
tags:: #almacenamiento
fecha:: 2026-06-05
links::
links_externos::

Cristaldisk info
Victoria

## Disco 1
### CristalDiskInfo

Descargamos y abrimos esta herramienta que de un primer pantallazo te da la información de salud de la unidad de almacenamiento. En este caso analisamos un disco HDD de Western Data NAS rojo.

![[Analisis_disco_crystaldiskinfo_0.png]]

Como se ve en la imagen podemos comprobar que el disco es nuevo, tiene 1 vez de encendido. El estado es Bueno que quiere decir lo más optimo en términos de salud que categoriza esta herramienta, por lo tanto con esta primera impresión pasamos a la segunda revisión.
### Victoria

Con esta herramienta vamos a probar primer con un Full Scan en modo Lectura, si tenemos unidades lógicas LBA dañadas. Cuando se hace un escaneo de la unidad, verificar los recuadros de colores segun el tipo de unidad

![[Analisis_disco_victoria_0.png]]


### Análisis

![[Analisis_disco_victoria_1.png]]

**Grafica**

La caída gradual que empieza cerca de **213 MB/s** y termina alrededor de **91 MB/s** es el comportamiento **normal** de un disco mecánico HDD.  Los discos giran a una velocidad constante, pero los sectores exteriores (donde empieza el escaneo) tienen más circunferencia y pasan más datos por segundo bajo el cabezal que los sectores internos (el final del disco).
 
 **El "ruido" (picos):** Esas oscilaciones en zigzag son comunes, especialmente si el disco tiene el sistema operativo corriendo o si hay procesos en segundo plano accediendo ligeramente a la unidad durante el test. No representan una falla física.   

**Celdas**

1- **Blanco**, la gran mayoría están en estado optimo.
2- **Gris claro** es buena pero no excelente, son pocas igual en referencia a la totalidad
3- **Gris oscuro**, estas ya están en estado regular, pero es una cantidad de 59, insignificante para las casi 8 mil millones de celdas.
4- No hay celdas en colores, lo que indica que no hay celdas dañadas

## Disco 2

### CristalDiskInfo

![[Analisis_disco_crystaldiskinfo_1.png]]

### Victoria

![[Analisis_disco_victoria_2.png]]
