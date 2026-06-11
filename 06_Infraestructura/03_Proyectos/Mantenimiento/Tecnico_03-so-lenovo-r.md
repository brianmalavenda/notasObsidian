tipo:: #tecnico/reparacion 
tags:: 
fecha:: 2025-09-22
links:: [[Lenovo]]
links_externos::

---
nombre::seba
dispositivo:: [[Lenovo]]
sistema:: [[Windows_11]]
ram::2gb
rom::256gb
- [ ] nueva
diagnostico:: el sistema operativo tarda mucho en iniciar
que_hacer::cambiar RAM, tiene 2gb  y la queremos extender a 8 GB
- [ ] reparada


Acciones: vamos a cambiarle el sistema operativo por un ubuntu 22.04 (linux). Tenemos que instalar el programa Open Office, Acrobar Reader, Brave, VPN?
Vamos a cambiarle los DNS que utiliza por los de alibaba - Baidú tiene? vamos a probar esos?


![[sb_notebook_lenovo_ramsoldada_1.jpg]]

![[sb_notebook_lenovo_ramsoldada_2.jpg]]


Pasamos a montar en un USB la distribución de Ubuntu 24.04 con la ISO que descargamos de https://ubuntu.com/download/desktop 

![[infraestructura_boot_so.png]]


Antes de fomatear la maquina Windows, comprobamos que tengamos el bitlocker desactivado. Primero verificamos que unidades tenemos y si estan con bitlocker o no. Para eso ejecutamos el comando

```
	manage-bde -status
```

![[infraestructura_boot_so_01.png]]

En este caso, ninguna unidad de almacenamiento o volumen, tiene activado bitlocker. Si alguna unidad lo tuviera, deberíamos ejecutar

```
	manage-bde -off [UNIDAD:]
```

---
#### Referencias

