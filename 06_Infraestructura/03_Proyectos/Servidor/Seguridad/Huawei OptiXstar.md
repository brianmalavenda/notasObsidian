tipo::
tags::
fecha:: 2026-03-27
links::
links_externos::

Desactivar UPnP

Esta opción asigna y abre puertos de manera automática, la deshabilitamos para tener control total de los puertos abiertos y como se comunican entre sí en la red local y hacia internet. 

Advance -> Application -> UPnP


## Cambiar gateway

En LAN -> Gateway

Cambiamos la subred del router. Esto te desconecta de internet hasta que cambie la IP de la PC administradora por la nueva subred para que pueda encontrar el router. Una vez hecho eso, la PC volverá a tener internet y accederemos al router desde cualquier navegador a través del nuevo gateway.
Esto hay que cambiarlo en todos los dispositivos que tengan configurado el viejo gateway, por ejemplo el servidor de sandbox.

#### Windows

Buscar "Estado de red" -> Propiedades -> Configuracion de IP , allí actualizar A "Manual" y cambiar la IP por una dentro de la nueva subred y cambiar el gateway por la nueva IP.

#### Linux (Proxmox)

En la consola ejecutar la línea ```nano /etc/network/interfaces``` para modificar el archivo que contiene la configuracion de red. Cambiar el gateway y la ip. Luego reiniciar el servicio de red con ```systemctl restart networking```.


Una vez hecho esto tanto la PC administradora como el servidor sandbox van a tener conexion a internet. Para probar el servidor, entrar a un navegador y poner el nuevo gateway con el puerto 8006.

### Configurar DHCP Estático

Entrar en el router a Advanced -> LAN -> DHCP Static IP

Ahí deberemos agregar los dispositivos que se van a conectar al gateway, para eso tenemos que saber la MAC Addres o la dirección física del dispositivo.

En **Windows** eso se puede hacer desde la consola con ```ipconfig /all``` y desde **Linux** con ```ip a```
donde la MAC esta en el campo "link/ether" de **nic0**.


### Configurar Port Forwarding

Antes de iniciar este paso deben estar creadas las VLAN's en el switch. 


---
#### Referencias

