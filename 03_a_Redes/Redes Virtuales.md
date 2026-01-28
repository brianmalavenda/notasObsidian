tipo::
tags::
fecha:: 2026-01-20
links::
links_externos::


- **VLAN 1 (Nativa/Management):** Solo para gestión del switch. Le cambiaremos el ID por seguridad. 
- **VLAN 10 (Admin/Trusted):** Donde estará tu **PC Admin** y quizá tu laptop personal. Tiene acceso a todo.
- **VLAN 20 (Producción):** Para el **Server Producción**. Muy restrictiva.
- **VLAN 30 (Sandbox/Desarrollo):** Para el **Server Sandbox**. Aislada de producción.
- **VLAN 99 (Hogar/Guest):** Para el tráfico general de tu casa (wifi, smartTVs). **Sin acceso** a las VLANs de servidores.


PC Admin : 192.168.1.10
Switch : 192.168.1.100


## Configuración del switch

![[Pasted image 20260120150441.png]]

Pongamos un ejemplo con la VLAN 20 la que corresponde a la PC administradora. El puerto 1 es a través del cual mi switch se conecta al router, por lo tanto de allí recibe el trafico **Tagged** (etiquetado), mi pc admin envía al router trafico **Untagged** (sin etiquetar) y el resto de los puertos están aislados. Es decir, mi pc admin no podría ver directamente a mis servidores. Para acceder a ellos lo hará a través del router que tendrá reglas de firewall para reenviar el tráfico de la VLAN 20 (ADMIN) a VLAN 10 (SERVERS)



---
#### Referencias

