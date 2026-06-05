tipo::
tags::
fecha:: 2026-05-22
links::
links_externos::

# Cómo duplicar un entorno WordPress en Docker (Staging) usando Portainer

En el mundo del desarrollo web y la administración de servidores de infraestructura (Homelabs o entornos Enterprise), contar con un entorno de pruebas independiente (**Staging**) es un requisito crítico. Nos permite probar actualizaciones de plugins, modificaciones en el código del tema o cambios estructurales sin correr el riesgo de romper el sitio web en producción.

Si gestionás tu infraestructura con **Docker** y **Portainer**, clonar un stack completo de WordPress es un proceso directo, pero requiere prestar especial atención a la persistencia de datos (volúmenes), la conectividad de red y la lógica interna de redirecciones de WordPress.

En esta guía técnica paso a paso, veremos el procedimiento definitivo para replicar un entorno de WordPress utilizando de manera eficiente las herramientas nativas de Docker, Portainer y la terminal.

## Arquitectura de Partida vs. Arquitectura de Destino

Antes de ejecutar comandos, es vital entender cómo se comunican las entidades. En Docker, cada Stack define una red aislada (un puente de red virtual o `bridge`). Al duplicar el entorno, clonamos la infraestructura lógica, pero debemos aislar las variables para que el tráfico no se cruce hacia producción.

|**Parámetro**|**Entorno de Producción**|**Entorno de Staging (Duplicado)**|
|---|---|---|
|**Nombre del Stack**|`wordpress-prod`|`wordpress-staging`|
|**Nombre Servicio BD**|`db`|`db_stagging`|
|**Nombre Servicio App**|`wordpress`|`wordpress_stagging`|
|**Puerto en el Host**|`8080:80`|`8081:80`|
|**Red Docker**|`wordpress_net`|`wordpress_net_stag`|
|**Ruta Base Volúmenes**|`/docker/wordpress/`|`/docker/wordpress_stagging/`|

## Paso 1: Duplicar la estructura de archivos en el Servidor (Host)

WordPress almacena los archivos estáticos (imágenes, temas, plugins en `wp-content`) y los archivos de configuración en el disco físico del Host de Docker.

1. Conectate por SSH a la terminal de tu servidor.
    
2. Duplicá la estructura de directorios de producción hacia la nueva ruta destinada al entorno de pruebas:
    

Bash

```
cp -r /docker/wordpress /docker/wordpress_stagging
```

> ⚠️ **Importante:** Al realizar una copia en caliente (`hot copy`) de los archivos, el directorio de la base de datos (`/data`) se copia en un estado "sucio" o inconsistente. Para evitar tablas corruptas o bloqueos de InnoDB en MariaDB, **vaciaremos la carpeta de datos de staging** para poblarla más adelante con un volcado SQL limpio:

Bash

```
sudo rm -rf /docker/wordpress_stagging/data/*
```

## Paso 2: Configurar el nuevo Stack en Portainer

1. Dirigite a tu panel de **Portainer** > **Stacks** > **Add stack**.
    
2. Nombrá al stack de forma descriptiva: `wordpress-staging`.
    
3. En el editor web (Web editor), definí el nuevo archivo `docker-compose.yml`. Aquí ajustaremos los nombres de contenedores, puertos y volúmenes para que apunten a la ruta física `/docker/wordpress_stagging`:
    

YAML

```
version: '3.8'

services:
  db_stagging:
    image: mariadb:10.11
    container_name: wordpress_stag_db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: tu_password_segura
      MYSQL_DATABASE: wordpress
      MYSQL_USER: sanmartin
      MYSQL_PASSWORD: tu_password_segura
    volumes:
      - /docker/wordpress_stagging/data:/var/lib/mysql
    networks:
      - wordpress_net_stag

  wordpress_stagging:
    image: wordpress:latest
    container_name: wordpress_stag_app
    restart: unless-stopped
    depends_on:
      - db_stagging
    environment:
      WORDPRESS_DB_HOST: db_stagging:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: sanmartin
      WORDPRESS_DB_PASSWORD: tu_password_segura
    ports:
      - "8081:80"
    volumes:
      - /docker/wordpress_stagging/html:/var/www/html
    networks:
      - wordpress_net_stag

networks:
  wordpress_net_stag:
    driver: bridge
```

4. Despliega el stack haciendo clic en **Deploy the stack**. Al estar la carpeta `/data` vacía, MariaDB se iniciará de manera totalmente limpia y configurará las credenciales correctamente.
    

## Paso 3: Exportar e Importar la Base de Datos de manera limpia

Para transferir los datos actuales de producción a staging sin romper permisos de red internos de la base de datos, realizaremos un volcado lógico (Dump).

1. **Exportar desde Producción:** Generá el archivo de respaldo ejecutando el comando directamente en el contenedor de base de datos de producción:
    
    Bash
    
    ```
    docker exec -it wordpress_db mysqldump -u sanmartin -p'tu_password_segura' wordpress > /tmp/backup_prod.sql
    ```
    
2. **Importar a Staging:** Inyectá el archivo `.sql` generado dentro del nuevo contenedor de base de datos de pruebas:
    
    Bash
    
    ```
    docker exec -i wordpress_stag_db mysql -u sanmartin -p'tu_password_segura' wordpress < /tmp/backup_prod.sql
    ```
    

## Paso 4: Resolver el bucle de redirección (HTTP 301) en la Base de Datos

Si intentás ingresar a la IP de tu servidor en el puerto `8081` en este momento, notarás que WordPress te redirige automáticamente a la URL o puerto de producción (`8080`). Esto ocurre porque la base de datos clonada conserva los registros antiguos en la tabla de opciones.

Dado que las imágenes estándar de WordPress no incluyen `WP-CLI` preinstalado de forma nativa (lo que arrojaría el error `executable file not found`), realizaremos la corrección directamente inyectando sentencias SQL en MariaDB:

1. Accedé a la consola de MariaDB del entorno de pruebas:
    
    Bash
    
    ```
    docker exec -it wordpress_stag_db mysql -u sanmartin -p'tu_password_segura' wordpress
    ```
    
2. Ejecutá las siguientes consultas para actualizar las directivas de localización (`home` y `siteurl`) con la dirección IP y el puerto correcto de Staging:
    
    SQL
    
    ```
    UPDATE wp_options SET option_value = 'http://10.0.37.13:8081' WHERE option_name = 'home';
    UPDATE wp_options SET option_value = 'http://10.0.37.13:8081' WHERE option_name = 'siteurl';
    ```
    
    _(Nota: Si usabas un prefijo de tablas personalizado diferente de `wp_`, modificá el nombre de la tabla por `tu_prefijo_options`)._
    
3. Salí de la consola de la base de datos:
    
    SQL
    
    ```
    exit
    ```
    
## Paso 5: Mitigación de Caché y Verificación

Los navegadores modernos almacenan las redirecciones de tipo **HTTP 301** de forma agresiva en su memoria caché local.

Para comprobar el éxito del despliegue:

1. Abrí una ventana en **Modo Incógnito** (o usá una sesión de navegador limpia).
    
2. Entrá directamente a la dirección del panel de administración: `http://10.0.37.13:8081/wp-login.php`.
    

¡Listo! A partir de este momento, tenés un gemelo idéntico de tu WordPress corriendo de forma aislada, controlado visualmente a través de tus Stacks de Portainer y listo para realizar pruebas intensivas de forma segura.


---
#### Referencias

