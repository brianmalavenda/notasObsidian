tipo::
tags::
fecha:: 2026-03-26
links::
links_externos::

Pass: el servidor
Email: sm_serv902@mail.sanmartinici.com.ar
Hostname: sanmartin902.local

## PASO 1: Crear VMs

| Prioridad | VM          | SO a descargar    | Tamaño  | ¿Crítico ahora? |
| --------- | ----------- | ----------------- | ------- | --------------- |
| 1         | AdGuard DNS | Debian 12         | ~400 MB | Sí              |
| 2         | TrueNAS     | TrueNAS SCALE ISO | ~1.2 GB | Sí              |
| 3         | Jump Server | Ubuntu 22.04      | ~1.2 GB | Sí              |
| 4         | Nextcloud   | Ubuntu 22.04      | ~1.2 GB | Sí              |
| 5         | WordPress   | Ubuntu 22.04      | ~1.2 GB | Sí              |
Cada VM implica toda una configuracion particular, no podemos avanzar al siguiente paso sin eso.

## PASO 2: Redes
Configurar red de seguridad

## PASO 3: Cluster
Crear un cluster, se puede? simular otro servidor Proxmox haciendose pasar por Prod



---
#### Referencias

