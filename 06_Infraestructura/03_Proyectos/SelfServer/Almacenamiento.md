tipo::
tags::
fecha:: 2025-10-31
links::
links_externos::

# Tipos de RAID

## RAID 0 
Se particiona la información en la cantidad de discos que haya. Cuando se pierde uno de los discos se pierde toda la información porque pierde un "cachito" de la referencia de la información de uno de los X discos que haya y se corrompe la totalidad de la info.

## RAID 1
Son discos espejos, es decir, tengo la información en el disco N1 y se copia la información  exactamente igual a otro disco N2, es decir tenemos pares de discos de igual tamaño los pares. Es mas lento en la escritura y perdemos la mitad de la capacidad de almacenamiento en la copia. Esto puede hacerse con pares de RAID's 0, es decir, tengo en mi RAID 0-1 (disco 1 y 2) y en mi segundo RAID 0-2 (disco 3 y 4), bueno tengo la información original en el 1 y 2 y la copia en el 3 y 4 de todo el raid 0-1.

## RAID 5 
Paridad de datos. Se suele trabajar con 4 discos donde 1 tiene la paridad, es decir el almacenamiento del calculo de la información que esta repartida en los otros 3 discos. 

## RAID 1+0
Este RAID anidado es el más utilizado porque ofrece seguridad, tolerancia a fallos y rapidez de lectura. Mezcla los conceptos del RAID 0 y el 1. Tenemos un par de discos en RAID 1-1 que tiene X información duplicada, es decir, en espejo y tenemos otro par de discos, RAID 1-2 donde hay información Y duplicada. Si falla un disco de algunos de los RAID 1 siempre esta el disco espejo para respaldar la información y no perderla. El RAID 0 que agrupa ambos RAID 1 nos permite leer en los dos RAID 1 a la vez.

![[tipos de raida - raid 10.png]]


# Sistema de archivos

## Ext4

## ZFS

Un sistema de archivos a prueba de fallos con comprobación de fiabilidad de datos. Tiene snapshot, copy on rigth. En tema de degradación de discos como en los RAID 5 funciona para asegurar que no se degraden

## BTRFS

Tiene snapshot, copy on rigth. En tema de degradación de discos como en los RAID 5 funciona para asegurar que no se degraden