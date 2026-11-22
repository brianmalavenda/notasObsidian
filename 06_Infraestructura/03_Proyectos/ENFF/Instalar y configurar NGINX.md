tipo::
tags:: #servidor/guia_instalacion 
fecha:: 2026-10-20 
links::
links_externos::


Instalar NGINX en un contenedor LxC en proxmox

```bash
apt update && apt upgrade -y
apt install -y curl apt-transport-https ca-certificates gnupg lsb-release

# Añadir repositorio oficial de Docker
mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# echo "deb [arch=$(dpkg --architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list

apt update
apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

la carpeta donde instalemos nginx va a ser como en todas las VM, ~/docker/nginx. Allí crearemos un  ````docker-compose.yml```
```yaml
version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'      # Puerto para el panel administrativo web
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

luego de tener esto levantamos el contenedor descargando la imagen de nginx y deployandola en segundo plano ```docker compose up -d```
El puerto **81** (`http://IP-DEL-LXC:81`) con las credenciales por defecto (`admin@example.com` / `changeme`) y ahí podemos empezar a rutear tus dominios o IPs locales hacia AdGuard u otros servicios.

Hay que configurar la ip fija para este contenedor LxC para que, si es que se apaga, cuando se recupere no cambie la IP y no perder el reverse proxy para el control de los puertos.

>[!NOTA] USUARIO
full name: enff
email: ti@enff.com
contraseña: enff2026

### Poner ip fija en un contenedor LxC

- Entra a la interfaz web de **Proxmox**.
- Haz clic sobre tu contenedor LXC de Nginx en el menú de la izquierda.
- Ve a la pestaña **Network** (Red).
- Verás la interfaz de red principal (generalmente llamada `eth0`). Haz doble clic sobre ella (o selecciónala y dale a _Edit_).
- Cambia el modo de IPv4 de **DHCP** a **Static**.
- Rellena los campos obligatorios:
    - **IPv4/CIDR:** La IP estática que deseas para tu Nginx junto con su máscara (por ejemplo, `192.168.1.50/24` —asegúrate de que esté fuera del rango que reparte el DHCP automático de tu router Ubiquiti para evitar conflictos de IP duplicada).
    - **Gateway (IPv4):** La IP de tu router Ubiquiti (por ejemplo, `192.168.1.1`).


>[!NOTA] NOTA
La IP fija que a elegir (ej. `192.168.1.50`) ** debe estar excluida del rango DHCP** configurado en tu router Ubiquiti. Por ejemplo, si tu router reparte IPs automáticas del `192.168.1.10` al `192.168.1.200`, entonces se peude asignar a tus servidores estáticos (AdGuard, Nginx) IPs bajas del rango (ej. `192.168.1.5` o `192.168.1.10`) para evitar que el router le asigne por error esa misma IP a otro dispositivo que se conecte a la red.


## Configurar los registros DNS en AdGuard Home

- Entra al panel de administración de tu **AdGuard Home**.
- Ve al menú lateral: **Filtros** -> **Reescrituras DNS** (o _DNS Rewrites_).
- Haz clic en **Añadir reescritura** (_Add rewrite_):
    - **Dominio:** `ejemplo.com` (o el subdominio que quieras, ej. `adguard.ejemplo.com`).
    - **Dirección IP:** La **IP local de tu contenedor LXC/VM donde corre Nginx**.
- Guarda los cambios.

### Crear un proxy host server

- Ve a **Proxy Hosts** -> **Add Proxy Host**.
- Completa los datos:
    - **Domain Names:** `ejemplo.com`
    - **Scheme:** `http`
    - **Forward Hostname / IP:** La IP interna de la máquina donde corre la aplicación (o AdGuard).
    - **Forward Port:** El puerto interno de la app (por ejemplo, `80` o `8080` si es AdGuard)        
    - _(Opcional)_ En la pestaña **SSL**, puedes solicitar un certificado _Let's Encrypt_ si tienes dominio público, o dejarlo en HTTP plano si es estrictamente para uso local interno (`http://`).

>[!NOTA] DNS
>Poner en configuración manual de DNS la ip donde esta corriendo Adguard: 10.20.21.241

## Configurar el DHCP en Ubiquiti

- Vas a _Settings_ (Configuración) > _Networks_ (Redes).
- Editas la red (o VLAN) correspondiente.
- En la sección de **DHCP Service**, buscas **DHCP Name Server**.
- Cambias la opción de "Auto" a **Manual** y colocas la **IP exacta de tu servidor AdGuard**.
- Guardas los cambios. A partir de ese momento, cada dispositivo que renueve su IP recibirá la dirección de AdGuard como su DNS primario.

## Configurar el router para conexion entre VLAN's

Dependiendo de si usas el panel clásico de UniFi o el nuevo sistema de _Traffic Rules_, necesitas permitir los siguientes flujos de tráfico:

1. **Permitir consultas DNS (AdGuard):**
    - **Origen (Source):** Tus redes/VLANs de usuario (ej. IoT, Invitados, Dispositivos personales).
    - **Destino (Destination):** La IP fija de tu servidor donde corre AdGuard.
    - **Puerto/Protocolo:** Puertos `53` (TCP y UDP).
        
2. **Permitir tráfico web (Nginx Reverse Proxy):**
    - **Origen (Source):** Las VLANs desde donde quieras acceder a tus apps.
    - **Destino (Destination):** La IP de tu LXC/VM con Nginx.        
    - **Puerto/Protocolo:** Puertos `80` (HTTP) y `443` (HTTPS).

_Consejo de arquitectura:_ Asegúrate de colocar estas reglas de "Permitir" **antes** de las reglas generales de bloqueo (Drop/Reject) que suelen venir por defecto en las políticas de _LAN In_ o _Inter-VLAN routing_ de Ubiquiti, de lo contrario el router descartará los paquetes.



---
#### Referencias

