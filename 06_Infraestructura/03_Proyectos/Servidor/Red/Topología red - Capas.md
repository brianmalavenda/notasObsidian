tipo::
tags:: #redes 
fecha:: 2026-06-12
links::
links_externos::

![[Pasted image 20260612110821.png]]

**Cloudflare** protege tus servicios públicos desde afuera — nadie llega directo a tu IP, todo pasa por su red primero.

**VLANs + firewall** garantizan que aunque alguien comprometa la DMZ, no puede moverse lateralmente hacia tu infraestructura de administración.

**WireGuard** cifra todo el tráfico de los empleados remotos de punta a punta, sin exponer puertos innecesarios.

**AdGuard** es la última milla — actúa antes de que cualquier conexión maliciosa se establezca, cortándola en el DNS.

**Authelia o Authentik** para poner MFA delante de los paneles de administración (Portainer, AdGuard, wg-easy) — así aunque alguien llegue a la VLAN ADMIN, no entra sin segundo factor.

---
#### Referencias

