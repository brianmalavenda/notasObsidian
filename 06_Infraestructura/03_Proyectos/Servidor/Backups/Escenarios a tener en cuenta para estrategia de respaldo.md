tipo::
tags::
fecha:: 2026-06-02
links:: 
links_externos::

## Infraestructura

**1) Proxmox**: Servidor principal
1. maquina virtual con una nube nextcloud (data de usuarios) y sitios web (con informacion de usuarios, contatos)
2. maquina vitual con apps de administracion, entre ellas TrueNAS
3. contenedor LxC de nginx
**2) PBS**: Netbook que hace de server de backups
**3) PC Admin**: PC utilizada para la administración de mi homelab
## Recursos

1. **DISCOS A1 y A2**: tengo 2 discos de 4TB en mi servidor que corre PVE
2. **DISCOS B1 y B2**: tengo 2 discos de 1 TB NVME (1 esta dedicado a PVE, sus maquinas virtuales etc y el otro por ahora no lo uso para nada)
3. **DISCO C1**: tengo 1 disco de 1 TB instalado en mi servidor PBS (netbook)
4. **DISCO D1**: tengo 1 disco de 1 TB instalado en mi PC ADMIN
5. **DISCO E1**: disco desconectado para backups en frío

## Escenarios
### 1) Falla total en el Servidor (Mueren A1, A2, B1, B2). Queda el DISCO C (PBS).

  - Si los 4 GB de datos de tus usuarios de Nextcloud se guardan **dentro** del disco virtual de la VM (que vive en el NVMe B1), **sí**, PBS los tiene salvados en el DISCO C. Pero si en tu Nextcloud configuraste que los datos se guarden directamente en el dataset del Pool de TrueNAS (en los discos mecánicos A1/A2) y pasas esa carpeta por red (SMB/NFS), **PBS no respalda carpetas de red externas**. Si mueren los discos A, perderías la data de Nextcloud.

>[!NOTA]
_Tu configuración de PVE:_ El DISCO C **no** la salva por defecto a menos que dejes programado el script en el `crontab` que envíe la carpeta `/etc/pve` al PBS.
  
### 2) Falla en DISCO C (PBS). A y B siguen vivos.

- El sistema de producción sigue online. Lo único que perdiste es el _historial_ de backups (puntos de restauración anteriores). Solo tienes que cambiar el disco físico de la netbook, reinstalar PBS y volver a empezar los jobs.
    
### 3) Falla total en A, B y C. ¿Salva la copia manual en el DISCO D (PC ADMIN)?

- Para que el DISCO D de tu PC ADMIN te salve ante un desastre atómico, lo que debes copiar en el DISCO D no son los chunks del PBS, sino los **backups tradicionales (`.tar.zst`) generados semanalmente en tu carpeta `share_data` de TrueNAS, o exportar la base de datos de los chunks**. Si tienes el archivo plano en tu PC, estás 100% a salvo.

>[!WARNING]
>Como el DISCO C (PBS) guarda los datos fragmentados en _chunks_ binarios, no puedes simplemente "copiar y pegar" archivos desde el navegador.

### 4) Falla el disco A1, queda A2.
    
- El ZFS Mirror en TrueNAS hace su trabajo en tiempo real. Cambias el disco roto por uno nuevo, el pool hace _resilver_ (reconstrucción) y la producción nunca se enteró.
    
### 5) El Ataque de Ransomware (Secuestro de datos)

- **El Riesgo:** Un atacante explota una vulnerabilidad en tu WordPress expuesto en la DMZ. Logra escalar privilegios y toma control de tu Proxmox o de tu TrueNAS, encriptando los discos A1, A2 y B1. **Tu Resguardo:** Gracias a que tu PBS (DISCO C) está aislado en la **VLAN BKP** con reglas estrictas de firewall y el tráfico hacia la red ADMIN está bloqueado, el malware **no puede llegar físicamente a la netbook**. Tus backups están a salvo de la infección.

### 6) Falla en el NVMe principal (DISCO B1 - Muerte de PVE y las VMs)

- **El Riesgo:** El disco NVMe donde corre Proxmox y las configuraciones del sistema operativo muere por desgaste de escritura. TrueNAS (VM 100) y tus aplicaciones productivas se apagan instantáneamente. Tus discos de datos de 4TB (A1 y A2) están intactos pero inaccesibles porque la controladora virtualizada murió.**Tu Resguardo:** Como tienes el PBS (DISCO C) intacto, compras un NVMe nuevo, reinstalas Proxmox VE en limpio, conectas el PBS mediante el _Fingerprint_, y le das "Restaurar" a la VM de TrueNAS y tus apps. En menos de una hora estás online de nuevo.

---
#### Referencias

