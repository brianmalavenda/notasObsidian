tipo::
tags::
fecha:: 2026-04-03
links::
links_externos::

### 1. El acceso al "Cerebro" (Proxmox GUI / Puerto 8006)

Cuando configuras la IP `192.168.10.10` en Proxmox, estás definiendo la puerta de entrada para **gestionar el servidor físico** (crear VMs, ver discos, apagar el equipo).

- **Quién entra:** Solo tú desde tu PC de administración (VLAN 10).
    
- **Por dónde:** Va directo de tu PC al Switch y al Proxmox. No necesita pasar por el Jump Server porque **tú eres el dueño de la infraestructura** y estás en la red segura de administración.
    

### 2. El acceso a las "Aplicaciones" (SSH de las VMs / Consola)

Aquí es donde entra tu idea del **Jump Server**.

- **El Objetivo:** Tú quieres que para entrar a la terminal de WordPress o de Nextcloud, sea obligatorio pasar por el Jumper.
    
- **Cómo se logra:** Las VMs (WordPress, etc.) están en la **VLAN 20**. Tu PC está en la **VLAN 10**. El Router tiene una regla que dice: _"Prohibido el paso de VLAN 10 a VLAN 20, EXCEPTO si la IP de origen es la del Jump Server"_.

**El escenario de seguridad total que tú buscas quedaría así:**

1. **Desde tu PC Admin (192.168.10.100):**
    
    - Puedes entrar a `https://192.168.10.10:8006` para ver Proxmox (porque estás en la misma VLAN de gestión). **Esto es seguro.**
        
2. **Desde tu PC Admin hacia el Sandbox (VLAN 40):**
    
    - Intentas hacer `ssh 192.168.40.50` -> **EL ROUTER TE BLOQUEA.**
        
    - Entras por SSH al **Jump Server (192.168.10.50)**.
        
    - Desde adentro del Jump Server, haces `ssh 192.168.40.50` -> **EL ROUTER TE DEJA PASAR** (porque el origen es el Jumper).


---
#### Referencias

