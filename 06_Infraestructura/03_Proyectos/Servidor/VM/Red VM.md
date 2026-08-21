tipo::
tags:: #red #servidor
fecha:: 2026-08-18
links::
links_externos::

Al iniciar una VM hay que configurar que la IP sea estática, apagar el DHCP, definir el gateway de la VLAN por la que se comunica con la WAN y definir que servidor de DNS va a usar

# Interfaces

Para configurar el archivo interfaces hay que abrir con nano el archivo interfaces donde se define la configuracion de red de la maquina. 

```bash

nano /etc/network/interfaces

#Reiniciar servicio de red

systemctl restart networking 

# Despues de configurar el archivo hacer un 
ping 8.8.8.8 # verificamos salida a internet
nslookup google.com # verificamos que este OK el servidor DNS
```

Como debe quedar el **interfaces**
![[Pasted image 20260818074132.png]]

# resolv.conf

Este archivo define el servidor de DNS en tiempo real, por lo tanto si difiere del interfaces el DNS no va a andar.

```bash
nano /etc/resolv.conf

# Solo es ncesario poner la siguiente línea
# nameserver [IP SERVER DNS]

systemctl restart networking

nslookup google.com
```


---
#### Referencias

