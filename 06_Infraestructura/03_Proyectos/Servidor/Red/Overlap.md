tipo::
tags::
fecha:: 2026-04-16
links::
links_externos::


# ¿Qué es el overlap?

El overlap en redes (red superpuesta o overlay network) es una red virtual construida encima de otra red física subyacente (underlay), utilizando túneles lógicos para conectar nodos. Permite crear topologías personalizadas y servicios avanzados, como SD-WAN o VPNs, sin cambiar la infraestructura física.

Para el caso de la segmentación de redes, esta característica es un error, ya que estaríamos produciendo el efecto contrario de una **división** sino más bien es una **unión lógica**. 

## Configuración de mi switch

![[config_swtich_valns_2.png]]

**El problema:** Según tu tabla, en la **VLAN 1** (la de arriba de todo), los puertos **1 a 6 están todos como Untagged**.

- **Conflicto:** El puerto 5 no puede ser "Untagged" en la VLAN 1 y en la VLAN YACANTO al mismo tiempo de forma efectiva en este tipo de switches.
    
- **Solución:** Debes ir a la configuración de la **VLAN 1** y poner el **Puerto 5** como **"Not Member"**. Así, el puerto 5 pertenecerá _únicamente_ a YACANTO.
## El problema de la "Superposición" (Overlap)

Al tener a todos los dispositivos en la VLAN 1 y al mismo tiempo en sus propias VLANs (5, 30, etc.), estás creando una red con "doble personalidad":

- **Por qué funciona:** Tu PC recibe una IP porque el TP-Link la ve a través de la VLAN 1 (que no tiene etiquetas). Como el TP-Link tiene la IP `192.168.0.1` en la LAN principal, te da internet por ahí.
    
- **El riesgo:** Si todos son miembros de la VLAN 1, la **segmentación no existe**. Un invitado en la red "INVITADOS" podría hablar con tu servidor "YACANTO" usando la "puerta trasera" de la VLAN 1, saltándose todas las reglas de firewall que pongas en el futuro.

## ¿Cómo se comunican los dispositivos si no están en la VLAN 1?

Esta es la clave del **Ruteo Inter-VLAN**:

- Los dispositivos **no necesitan estar en la misma VLAN** para comunicarse entre sí a través del switch.
    
- Cuando un paquete sale de la red **YACANTO** hacia la red **HOGAR**, el paquete viaja por el puerto Trunk (Puerto 1 del switch) hacia el **TP-Link**.
    
- El **TP-Link** actúa como un "oficial de aduana": recibe el paquete de la VLAN 5, mira si tiene permiso para ir a la VLAN 210, y si es así, lo vuelve a bajar por el mismo cable hacia el switch con la nueva etiqueta.
    

> **En resumen:** La comunicación entre redes ocurre en el **Router**, no en el **Switch**. Si dejas que se comuniquen por el switch (vía VLAN 1), pierdes el control de seguridad sobre quién ve a quién.


### Cómo detectar el "Overlap" visualmente

Para ver si la segmentación está fallando, hacé esto en Wireshark:

1. Iniciá la captura en tu placa Ethernet.
    
2. En el filtro de arriba (barra verde), escribí: `arp or icmp`.
    
3. Si empezás a ver paquetes de **IPs que no son de tu rango** `192.168.5.x` (por ejemplo, ves un pedido ARP de la `192.168.0.20`), **¡ahí tenés la prueba!**
    
    - Eso significa que el "aire" de la VLAN 1 se está mezclando con el de tu red YACANTO. El switch le está entregando a tu PC paquetes que no le pertenecen.


![[wireshark_1.png]]


---
#### Referencias

