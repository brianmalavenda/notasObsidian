tipo::
tags:: #seguridad #seguridad/red 
fecha:: 2026-06-19
links::
links_externos::

### Vaultwarden: la opción correcta para tu setup

Vaultwarden (reimplementación en Rust del servidor de Bitwarden) es la elección casi obligada acá porque:

- Es liviano (corre perfecto en un contenedor LXC o Docker, consumo mínimo de RAM/CPU)
- Es compatible con todos los clientes oficiales de Bitwarden (apps móviles, extensiones de navegador, CLI)
- Se integra naturalmente con tu stack actual: NGINX como reverse proxy, Cloudflare para DNS/SSL, y eventualmente Authelia para MFA en la capa de acceso

#### Despliegue sugerido

Siguiendo tu convención de `/docker/[service]/[name]`:

yaml

```yaml
# /docker/vaultwarden/docker-compose.yml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    environment:
      - DOMAIN=https://vault.tudominio.com
      - SIGNUPS_ALLOWED=false  # importante: desactivar después de crear tu cuenta
      - WEBSOCKET_ENABLED=true
      - ADMIN_TOKEN=${ADMIN_TOKEN}  # generar con: openssl rand -base64 48
    volumes:
      - /docker/vaultwarden/data:/data
    ports:
      - "127.0.0.1:8080:80"  # solo localhost, NGINX hace el proxy
```

Puntos clave para tu arquitectura:

- **`SIGNUPS_ALLOWED=false`** una vez creado tu usuario — esto es crítico, sino cualquiera que llegue al subdominio puede registrarse
- Exponelo solo dentro de tu DMZ/WireGuard, no en la superficie pública directamente — encaja perfecto con el diseño que ya tenés (Cloudflare → Authelia → ER605 → DMZ)
- NGINX como reverse proxy con el mismo patrón que usás para Nextcloud

¿Querés que armemos también la config de NGINX y el bloque de Authelia para protegerlo con MFA, ya que es probablemente el servicio más sensible de todo tu homelab?

### El respaldo "en frío" legible — esto es lo más importante

Acá hay una distinción crucial que quiero que tengas clara: **el backup de la base de datos de Vaultwarden (sqlite/postgres) NO te sirve si el sistema está cifrado o el servidor no levanta**, porque necesitás la app corriendo para desencriptar los vaults. Para el escenario "caída total / ransomware" necesitás algo independiente de la infraestructura.

Opciones, de más a menos recomendada:

**1. Export cifrado + JSON plano impreso/guardado offline**  
Bitwarden/Vaultwarden permite exportar el vault completo como `.json` (texto plano, legible) desde el cliente. Esto es la app, no el servidor:

- Vault → Tools → Export Vault → formato `.json` (sin cifrar) o `.csv`
- Ese archivo se guarda en un pendrive cifrado con VeraCrypt o LUKS, **desconectado del sistema**, y opcionalmente con una copia impresa de las credenciales más críticas (root de Proxmox, claves maestras, etc.) en una caja fuerte física

**2. Backup automatizado del JSON export vía script**  
Podés automatizar el export con la API admin de Vaultwarden o con `bw` CLI corriendo en un cron, generando el JSON y copiándolo a un destino offline (no a Nextcloud, porque si cae el sistema cae todo junto).

**3. Regla 3-2-1 adaptada a "frío real"**

- 1 copia en el servidor (la DB operativa)
- 1 copia en Proxmox Backup Server (que ya tenés)
- 1 copia verdaderamente fuera de banda: pendrive/SSD externo desconectado físicamente la mayor parte del tiempo, actualizado manualmente cada cierto tiempo


---
#### Referencias

