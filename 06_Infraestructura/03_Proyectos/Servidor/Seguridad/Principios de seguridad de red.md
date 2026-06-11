tipo::
tags::
fecha:: 2025-10-17
links::
links_externos::

#### 1. Segmentación por VLANs (Tu Pilar de Seguridad)

No puedes tener todos los dispositivos (servidores, admin, invitados) en la misma red plana. Una brecha en WordPress no debería dar acceso directo a tus datos personales o a la configuración del servidor. La segmentación es la base de una red segura

- **VLAN 1 (Native/Management):** Reservada para la administración del switch y router. Solo accesible desde tu `pc_admin`.
- **VLAN 10 (DMZ - Publica):** Aquí irá tu **Reverse Proxy** (Nginx, Traefik, etc.). Es la única zona que mirará a internet. Si un atacante compromete el proxy, estará en una jaula de cristal sin acceso directo a tus datos.
- **VLAN 20 (Internal - Produccion):** Para tu `serv_1_prod` (Nextcloud, WordPress). Este servidor **no** tendrá una IP pública ni puertos abiertos directamente. Solo hablará con el Reverse Proxy de la VLAN 10.
- **VLAN 30 (Sandbox):** Para tu `serv_2_sandbox`. Zona aislada para pruebas.
- **VLAN 99 (Admin):** Para tu `pc_admin`. Con acceso a todas las VLANs, pero nadie puede iniciar conexión desde las otras VLANs hacia esta.

#### 2. El Paradigma del Reverse Proxy

Para exponer Nextcloud y WordPress de forma segura, necesitas un **Reverse Proxy** (como Nginx, Caddy o Traefik) [](https://qna.habr.com/answer?answer_id=2458778#answers_list_answer)[](https://serverfault.com/questions/1152738/getting-nextcloud-and-wordpress-image-behind-a-location-in-nginx?r=SearchResults)[](https://docs.nextcloud.com/server/15/admin_manual/configuration_server/reverse_proxy_configuration.html).

- **Flujo de trabajo:** Internet (Puerto 443) --> Router (Port Forward) --> **Reverse Proxy (VLAN 10)** --> Reverse Proxy decide el destino: si la petición es `nextcloud.tudominio.com` la envía a `serv_1_prod` (VLAN 20); si es `blog.tudominio.com` la envía al mismo servidor o a otro contenedor.
- **Ventajas:** Terminas el SSL/TLS en un solo lugar (el proxy) y mantienes tus servidores de aplicaciones aislados sin exponerlos directamente al mundo

#### 3. Políticas de Firewall (Reglas Clave)

Con las VLANs creadas, debes definir quién habla con quién:

- **Regla Maestra:** Denegar todo el tráfico entre VLANs por defecto.
- **Excepciones:**
    - **DMZ (VLAN 10) -> Produccion (VLAN 20):** Permitir solo tráfico desde la IP del Reverse Proxy hacia los puertos específicos de tus servicios (ej. 443 para HTTPS de Nextcloud, 80 para WordPress). Nada más.
    - **Admin (VLAN 99) -> Todo:** Permitir acceso desde tu PC a todas las VLANs para administración (SSH, HTTP de gestión).
    - **Internet -> DMZ:** Permitir solo tráfico entrante a los puertos 80 y 443 hacia la IP del Reverse Proxy.    
    - **Salida a Internet:** Permitir a todas las VLANs salir a internet (NAT), pero limitar la DMZ si no necesita actualizaciones automáticas.
        


![[config_switch_vlans.png]]

---

---
#### Referencias

