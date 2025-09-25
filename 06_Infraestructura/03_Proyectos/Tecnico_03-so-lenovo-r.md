tipo::
tags:: #tecnico/reparacion 
fecha:: 2025-09-22
links::
links_externos::
observacion:: Cambiar RAM, tiene 2gb  y la queríamos extender a 8 GB pero la tiene soldada a la placa.

Acciones: vamos a cambiarle el sistema operativo por un ubuntu 22.04 (linux). Tenemos que instalar el programa Open Office, Acrobar Reader, Brave, VPN?
Vamos a cambiarle los DNS que utiliza por los de alibaba - Baidú tiene? vamos a probar esos?


![[1.jpg]]

![[2.jpg]]


Pasamos a montar en un USB la distribución de Ubuntu 24.04 con la ISO que descargamos de https://ubuntu.com/download/desktop 

![[Pasted image 20250924075711.png]]


Antes de fomatear la maquina Windows, comprobamos que tengamos el bitlocker desactivado. Primero verificamos que unidades tenemos y si estan con bitlocker o no. Para eso ejecutamos el comando

```
	manage-bde -status
```

![[Pasted image 20250925092451.png]]

En este caso, ninguna unidad de almacenamiento o volumen, tiene activado bitlocker. Si alguna unidad lo tuviera, deberíamos ejecutar

```
	manage-bde -off [UNIDAD:]
```

---
#### Referencias

