tipo:: #servidor/red 
tags::
fecha:: 2025-11-13
links:: [[Cloudflare]] 
links_externos::


### **1. Instalar y configurar cloudflared services:**

```bash
# Descargar cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64 -O cloudflared

chmod +x cloudflared
sudo mv cloudflared /usr/local/bin/

# Autenticar (te abrirá una página web)
cloudflared tunnel login

```

### **2. Crear tunel:**

Antes de realizar este paso tenemos que tener un dominio el cual configurar para el tunelling. Para eso tiene que estar registrado en cloudflare y utilizar su servicios de DNS

```bash
# Crear tunnel
cloudflared tunnel create nextcloud-tunnel

# Configurar DNS
cloudflared tunnel route dns nextcloud-tunnel cloud.sanmartinici.com.ar

# Crear archivo de configuración
mkdir -p ~/.cloudflared
cat > ~/.cloudflared/config.yml << EOF
tunnel: nextcloud-tunnel
credentials-file: /root/.cloudflared/nextcloud-tunnel.json
ingress:
  - hostname: cloud.sanmartinici.com.ar
    service: http://localhost:8088
  - service: http_status:404
EOF
```

### **3. Ejecutar tunel:**

```bash
# Ejecutar en foreground para probar
cloudflared tunnel run nextcloud-tunnel

# O como servicio
sudo cloudflared service install
sudo systemctl start cloudflared

## **Después de configurar el Tunnel:**

### **Acceso:**
https://cloud.sanmartinici.com.ar  (SIN puerto)
```

#### **Verificación:**

```bash

# Desde fuera, sin puerto
curl -v https://cloud.sanmartinici.com.ar
# Debería mostrar Nextcloud
sudo cloudflared service install [token]

```


![[cloudflare tunnelling conectado.png]]


---
#### Referencias

