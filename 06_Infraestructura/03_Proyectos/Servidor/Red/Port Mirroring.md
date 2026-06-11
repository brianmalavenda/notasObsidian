tipo::
tags::
fecha:: 2026-04-17
links::
links_externos::


![[config_switch_vlans_port.png]]


![[wireshark_2.png]]

### 1. El "Grito" en el vacío (Líneas 1, 4, 7, 8...)

Fijate en esto:

- **Sagemcom (210.1)** pregunta: _"¿Quién tiene la IP 210.10?"_
    
- **Sagemcom (200.1)** pregunta: _"¿Quién tiene la IP 200.15?"_
    

**El problema:** Nadie responde. En un ARP normal, deberías ver una línea inmediatamente abajo que diga: _"192.168.210.10 is at [MAC Address]"_.



---
#### Referencias

