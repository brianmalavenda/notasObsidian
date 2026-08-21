tipo::
tags:: #nginx
fecha:: 2026-07-30
links::
links_externos::

### Caso A: Si se instaló en un LXC mediante Proxmox Helper Scripts (tteck / Community Scripts)

Si creaste el LXC con el instalador automatizado para Proxmox, el script incluye una rutina de actualización propia.

1. Abre la consola de la **LXC de Nginx Proxy Manager** (no la del nodo de Proxmox) y ejecuta simplemente:
    
    ```Bash
    update
    ```
    
1. Si la orden `update` no es reconocida, ejecuta directamente el script de mantenimiento actualizado:
    
    ```Bash
    bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/nginxproxymanager.sh)"
    ```

>[!NOTE] Nota
El script detectará que ya está instalado, detendrá los servicios (`npm`, `openresty`/`nginx`), actualizará el repositorio de NPM, construirá los paquetes de Node/Frontend necesarios y reiniciará el servicio sin borrar tus datos._   

### Caso B: Si se hizo una instalación nativa/manual en Debian/Ubuntu/Alpine

Si compilaste o instalaste NPM clonando el repositorio Git directamente en el LXC, la actualización se hace estirando los cambios más recientes del repositorio:

```Bash
# 1. Detener el servicio de NPM / OpenResty
systemctl stop openresty  # O systemctl stop nginx-proxy-manager según tu configuración

# 2. Ir al directorio donde clonaste NPM (habitualmente /app o /opt/nginx-proxy-manager)
cd /app

# 3. Traer los últimos cambios
git fetch --all
git checkout master
git pull

# 4. Actualizar dependencias de Node.js / Frontend
yarn install --frozen-lockfile # O pnpm install
yarn build                     # O pnpm build

# 5. Volver a iniciar el servicio
systemctl start openresty
```

>[!WARNING] Recomendación 
>Antes de ejecutar cualquier script o comando de actualización, realiza un **Snapshot** rápido del LXC desde la interfaz de Proxmox VE (_LXC > Snapshots > Take Snapshot_). Así, en caso de algún fallo de dependencias (por ejemplo Node.js o pnpm), puedes volver atrás al instante.


---
#### Referencias

