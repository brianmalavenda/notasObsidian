tipo::
tags::
fecha:: 2026-07-02
links::
links_externos::

#### Autostart de VMs/LXCs — nativo, sin script

Proxmox ya resuelve esto sin necesidad de nada custom:

bash

```bash
qm set <VMID> --onboot 1 --startup order=1,up=30,down=30
pct set <CTID> --onboot 1 --startup order=2,up=15
```

- `order` define secuencia de arranque entre VMs/LXCs
- `up=N` es el delay (segundos) que Proxmox espera antes de arrancar la siguiente en la cola — usalo para darle tiempo a que el servicio dentro de la VM esté realmente listo, no solo bootee el OS
- Esto corre automáticamente al bootear el host, no requiere cron ni systemd extra en el host

Convención sugerida para tu stack (monitoring → vaultwarden → resto):

1. Order 1: VM/LXC de red interna (AdGuard, WireGuard si corren en VM propia)
2. Order 2: Prometheus/Grafana/Loki (monitoreo primero, para ver qué pasa con el resto)
3. Order 3: Vaultwarden
4. Order 4+: resto de servicios/clientes

#### Docker stacks — se resuelve solo, si está bien configurado

No necesitás un script que "levante" los stacks después del boot de cada VM. Si tenés:

yaml

```yaml
restart: unless-stopped
```

en cada `docker-compose.yml`, y `docker.service` habilitado (`systemctl enable docker`, que es default en instalaciones normales), **los containers se levantan solos** cuando arranca el daemon de Docker, que arranca solo cuando bootea la VM.

Verificar en cada VM:

bash

```bash
systemctl is-enabled docker   # debe decir "enabled"
docker inspect <container> --format='{{.HostConfig.RestartPolicy.Name}}'
```

Si algún stack no tiene `restart: unless-stopped`, corregirlo antes del mantenimiento — ahí está el único punto de falla real.
---
#### Referencias

