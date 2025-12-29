tipo:: #servidor/web 
tags::
fecha:: 2025-11-14
links::
links_externos::

## Cómo actualizar Nextcloud dockerizado correctamente:

### 1. **Primero, haz backup** (IMPORTANTE)

bash

# Backup de volúmenes docker
docker-compose down
sudo cp -r /ruta/tus/datos/nextcloud /ruta/backup/
docker-compose up -d

### 2. **Actualizar la imagen de Docker**

bash

# Si usas docker-compose, edita el docker-compose.yml y cambia la versión
# Luego ejecuta:
docker-compose pull
docker-compose down
docker-compose up -d

### 3. **Ejecutar occ upgrade DENTRO del contenedor**

bash

# Encuentra el nombre de tu contenedor
docker ps

# Ejecuta occ upgrade dentro del contenedor
docker exec -u www-data NOMBRE_CONTENEDOR_NEXTCLOUD php occ upgrade

# O si necesitas ser root primero:
docker exec -it NOMBRE_CONTENEDOR_NEXTCLOUD bash
su - www-data -s /bin/bash
php occ upgrade
exit

### 4. **Verificar el estado**

bash

docker exec -u www-data NOMBRE_CONTENEDOR_NEXTCLOUD php occ status

## Recomendación adicional:

Si usas docker-compose, el proceso completo sería:

bash

cd /ruta/de/tu/docker-compose
docker-compose down
docker-compose pull
docker-compose up -d
docker exec -u www-data nextcloud_app_1 php occ upgrade


---
#### Referencias

