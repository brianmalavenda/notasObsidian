tipo::
tags::
fecha:: 2025-10-21
links::
links_externos::


Una vez que mi docker-compose lo configuré para entrar por el puerto 9090 y redirigir al puerto 80 del contenedor tengo luego que decirle al contenedor (desde su configuración internet que no esta "saliendo" por el puerto 8080 la aplicación sino por el 9090)

```bash
# Entrar al contenedor como usuario www-data
docker exec -u 33 -it nextcloud-app bash

# Una vez dentro del contenedor, ejecutar:
php occ config:system:get trusted_domains
php occ config:system:set trusted_domains 1 --value="192.168.0.10:9090"
php occ config:system:set overwritehost --value="192.168.0.10:9090"
php occ config:system:set overwriteprotocol --value="http"
php occ maintenance:repair
exit`
```


Usuario administrador
user: sanmartin-nextcloud
pass: Los andes1817#






---
#### Referencias

