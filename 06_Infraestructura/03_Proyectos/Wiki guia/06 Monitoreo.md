## 5. Monitoreo y Métricas en Proxmox VE

Para garantizar la estabilidad ante una alta concurrencia (como 200 usuarios consultando servicios web), es vital monitorear el rendimiento del hipervisor y los contenedores.

### A. Métricas Nativas e Integración con InfluxDB / Grafana

Proxmox incluye de manera nativa un sistema de métricas externo configurable desde la interfaz gráfica:

1. Ir al nodo principal en Proxmox → **Datacenter** -> **Metric Server**.
2. Añadir un servidor de métricas (por ejemplo, conectarlo a una instancia externa o local de **InfluxDB** y visualizarlo mediante **Grafana** para métricas detalladas de CPU, I/O de disco, uso de RAM y red por contenedor/VM).
    
### B. Proxmox Monitor / Dashboards de Comunidad (Grafana)

Para una visualización avanzada orientada a administradores de sistemas:

- Desplegar un contenedor LXC ligero en Proxmox con **Grafana** y **Prometheus** (o utilizar los scripts automatizados de la comunidad para instalar dashboards de monitoreo de Proxmox).
- El script de instalación de Grafana/Prometheus para PVE se puede desplegar rápidamente con:

    ```bash 
    -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/gr
    ```