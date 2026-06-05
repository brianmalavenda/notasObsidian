tipo::
tags::
fecha:: 2026-04-03
links::
links_externos::

| Prioridad | VM          | SO a descargar    | Tamaño  | ¿Crítico ahora? |
| --------- | ----------- | ----------------- | ------- | --------------- |
| 1         | AdGuard DNS | Debian 12         | ~400 MB | Sí              |
| 2         | TrueNAS     | TrueNAS SCALE ISO | ~1.2 GB | Sí              |
| 3         | Jump Server | Ubuntu 22.04      | ~1.2 GB | Sí              |
| 4         | Nextcloud   | Ubuntu 22.04      | ~1.2 GB | Sí              |
| 5         | WordPress   | Ubuntu 22.04      | ~1.2 GB | Sí              |

### VM 1: AdGuard DNS (la que estás creando ahora)

|Tipo|Recurso|Enlace de descarga|Tamaño aprox.|
|---|---|---|---|
|**SO**|Debian 12 (Bookworm) NetInst|[Debian 12 netinst](https://www.debian.org/distrib/netinst)|~400 MB|
|**App**|AdGuard Home|Se instalará por script dentro de la VM|-|
**Requisitos**: 1 vCPU, 1-2 GB RAM, 10 GB disco[](https://git.mdns.eu/nextcloud/passwords/-/wikis/Administrators/System-Requirements/diff?version_id=feaa0bb7f87c800c4b8acf373cc129d6c4b4bb8d&view=inline)

---
### VM 2: TrueNAS (Backups automatizados)

| Tipo   | Recurso           | Enlace de descarga                                                                   | Tamaño aprox. |
| ------ | ----------------- | ------------------------------------------------------------------------------------ | ------------- |
| **SO** | TrueNAS SCALE ISO | [truenas.com/download-truenas-scale](https://www.truenas.com/download-truenas-scale) | ~1.2 GB       |
**Requisitos mínimos**[](https://github.com/valentin-rossi-1/TrueNAS-Scale)[](https://forums.truenas.com/t/getting-started-with-virtualization-on-truenas-scale/1605/1):

- **CPU**: 2+ núcleos (4 recomendados)    
- **RAM**: 8 GB mínimo, 16 GB+ recomendado
- **Discos**: Al menos 2 discos dedicados para el pool ZFS (pueden ser virtuales en Proxmox, pero ideal pasar discos físicos por passthrough)
- **Almacenamiento boot**: 16 GB mínimo

> 💡 TrueNAS SCALE ya incluye funcionalidades de backup (replicación ZFS, Cloud Sync, SMB/NFS shares). Perfecto para backups de tus VMs[](https://forums.truenas.com/t/getting-started-with-virtualization-on-truenas-scale/1605/1).

---

### VM 3: Jump Server (Puerta de entrada segura)

| Tipo   | Recurso                 | Enlace de descarga                                               | Tamaño aprox. |
| ------ | ----------------------- | ---------------------------------------------------------------- | ------------- |
| **SO** | Ubuntu Server 22.04 LTS | [ubuntu.com/download/server](https://ubuntu.com/download/server) | ~1.2 GB       |
**Requisitos**: 1 vCPU, 512 MB-1 GB RAM, 8 GB disco
**¿Qué instalaremos dentro?**
- SSH con claves (acceso punto único) 
- Fail2ban (protección fuerza bruta)
- Posiblemente Tailscale o WireGuard

---

### VM 4: Nextcloud (Almacenamiento y colaboración)

|Tipo|Recurso|Enlace de descarga|Tamaño aprox.|
|---|---|---|---|
|**SO**|Ubuntu Server 22.04 LTS|[ubuntu.com/download/server](https://ubuntu.com/download/server)|~1.2 GB|
|**App**|Nextcloud|Instalación por script o manual|-|
**Requisitos según Nextcloud oficial**[](https://git.mdns.eu/nextcloud/passwords/-/wikis/Administrators/System-Requirements/diff?version_id=feaa0bb7f87c800c4b8acf373cc129d6c4b4bb8d&view=inline)[](https://docs.nextcloud.com/server/stable/admin_manual/installation/system_requirements.html?highlight=64bit#cpu-architecture-and-os):

| Recurso | Mínimo                       | Recomendado      |
| ------- | ---------------------------- | ---------------- |
| CPU     | 1 vCPU                       | 2+ vCPU          |
| RAM     | 512 MB                       | 4-8 GB           |
| Disco   | 10 GB                        | 64+ GB           |
| OS      | Debian 12 / Ubuntu 22.04 LTS | Ubuntu 22.04 LTS |
| PHP     | 8.2                          | 8.3              |
| DB      | MySQL/MariaDB/PostgreSQL     | MariaDB 10.11    |

---

### VM 5: WordPress (Tu sitio web)

|Tipo|Recurso|Enlace de descarga|Tamaño aprox.|
|---|---|---|---|
|**SO**|Ubuntu Server 22.04 LTS|[ubuntu.com/download/server](https://ubuntu.com/download/server)|~1.2 GB|
|**App**|WordPress + LEMP stack|Instalación manual|-|

**Requisitos recomendados**[](https://github.com/dsantra80/wordpress-setup)[](https://netcloud24.com/knowledgebase/how-to-install-wordpress-with-lemp-on-ubuntu-22-04/):

|Recurso|Mínimo|Recomendado|
|---|---|---|
|CPU|1 vCPU|2 vCPU|
|RAM|1 GB|2-4 GB|
|Disco|10 GB|30+ GB (crece con contenido)|
|LEMP stack|Nginx, MySQL, PHP 8.1+|Nginx + PHP-FPM + MariaDB|

---
#### Referencias

