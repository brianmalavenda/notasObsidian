tipo:: 
tags::
fecha:: 2025-12-23
links:: [[Nextcloud]]
links_externos::


# PASO 1:
 
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


```bash
# Deshabilitar editar texto en linea
docker exec -u 33 nextcloud-app php occ app:disable text
# Deshabilitar escritura colaborativa 
docker exec -u 33 nextcloud-app php occ app:disable richdocuments
# Deshabilitar visor documentos en linea
docker exec -u 33 nextcloud-app php occ app:disable viewer
# Deshabilitar servicio del tiempo
docker exec -u 33 nextcloud-app php occ app:disable weather_status
# Deshabilitar recomendaciones de archivos ("Quizás te interese...")
docker exec -u 33 nextcloud-app php occ app:disable recommendations
# Deshabilitar estado de usuario (Disponible/Ausente/Ocupado) y mensajes de estado
docker exec -u 33 nextcloud-app php occ app:disable user_status
# Desactivar la app de calendario
docker exec -u 33 nextcloud-app php occ app:disable calendar

# comandos para "aligerar" la carga de la app al servidor
 docker exec -u 33 nextcloud-app php occ maintenance:repair
 docker exec -u 33 nextcloud-app php occ maintenance:mode --off
 docker exec -u 33 nextcloud-app php occ maintenance:repair
```


### Configurar mi php.ini

```php
opcache.enable=1
opcache.memory_consumption=128
opcache.interned_strings_buffer=16
opcache.max_accelerated_files=10000
opcache.revalidate_freq=300
opcache.fast_shutdown=1
opcache.enable_cli=1
```

###  Configuración PHP-FPM para bajos recursos

```bash
docker exec nextcloud-app find / -name "*fpm*" -type f 2>/dev/null
# resultado
/var/www/html/3rdparty/thecodingmachine/safe/generated/fpm.php
/usr/src/nextcloud/3rdparty/thecodingmachine/safe/generated/fpm.php

```

```php
; En tu pool PHP-FPM
pm = dynamic
pm.max_children = 6
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3
pm.max_requests = 500

; Límites de memoria
php_admin_value[memory_limit] = 256M
php_admin_value[post_max_size] = 512M
php_admin_value[upload_max_filesize] = 512M
```

 **¿Por qué 256M es MEJOR en tu caso?**

1. **Tu contenedor tiene 512MB límite**:   
    - Con `memory_limit = 512M`: Cada proceso PHP puede usar hasta 512MB
    - Con 4-6 procesos PHP-FPM: `512MB × 6 = 3GB` ✅ **EXCEDE tu RAM disponible**
    - Con `memory_limit = 256M`: `256MB × 6 = 1.5GB` ✅ **DENTRO de tus límites**

2. **Nextcloud con 256M es suficiente**:
    - Operaciones normales: 64-128MB        
    - Operaciones pesadas (PDFs): 150-200MB
    - 256MB da margen sin excederse

### Cambiar configuración de mi php

```bash
docker exec nextcloud-app find / -name "php.ini" 2>/dev/null
#respuesta
/var/www/html/3rdparty/aws/aws-crt-php/php.ini
/usr/src/nextcloud/3rdparty/aws/aws-crt-php/php.ini

```


## Problemas de ejecución

>[!error] ERROR
>AH00558: apache2: Could not reliably determine the server's fully qualified domain name


```bash
# 2. CONFIGURAR APACHE DENTRO DE NEXTCLOUD
docker exec nextcloud-app bash -c 'echo "ServerName localhost" >> /etc/apache2/apache2.conf'
docker-compose restart nextcloud-app
```

## Desactivar funcionalidad de ver miniaturas de imagenes, videos y pfd's

**Desactivar**

```bash
docker exec -u 33 nextcloud-app php occ config:system:set enable_previews --value false --type boolean 
docker exec -u 33 nextcloud-app php occ config:system:set preview_max_x --value 0 --type integer 
docker exec -u 33 nextcloud-app php occ config:system:set preview_max_y --value 0 --type integer
```

**Rehabilitar**

```bash
docker exec -u 33 nextcloud-app php occ config:system:set enable_previews --value true --type boolean
docker exec -u 33 nextcloud-app php occ config:system:set preview_max_x --value 2048 --type integer
docker exec -u 33 nextcloud-app php occ config:system:set preview_max_y --value 2048 --type integer
```


## Configurar proxy para acceso por fuera de la red

```yml
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - "80:80"      # HTTP
      - "443:443"    # HTTPS
      - "81:81"      # Admin interface
    volumes:
      - ./nginx-proxy/data:/data
      - ./nginx-proxy/letsencrypt:/etc/letsencrypt
    networks:
      - nextcloud-net
```

comentar el puerto en la app porque ahora no expone la app del contenedor directamente el puerto sino que esta mediado por la administración en el proxy
#### Crear directorios para el proxy
mkdir -p nginx-proxy/data nginx-proxy/letsencrypt

Como en el puerto 80 tengo corriendo el servidor web de Open Media Vault vamos a cambiar los puertos de proxy por 

```yml
    ports:
      - "8080:80"      # HTTP
      - "8443:443"    # HTTPS
      - "8181:81"      # Admin interface
```


### Configuración del Proxy Manager

1. **Accede al admin:** `http://192.168.0.10:8181`
2. **Credenciales iniciales:**
    - Email: `admin@example.com`
    - Password: `changeme`
        
3. **Configurar proxy para Nextcloud:**
    - **Proxy Hosts** → **Add Proxy Host**
    - **Details Tab:**
        Domain Names: nextcloud.local
        Forward Hostname/IP: nextcloud-app  
        Forward Port: 80
        
    - **Advanced Tab:**
        nginx
        
### Para mejorar compatibilidad con Nextcloud

        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
        

```bash
# Añadir dominio local como confiable
docker exec -u 33 nextcloud-app php occ config:system:set trusted_domains 1 --value="nextcloud.local"

# Configurar headers para proxy
docker exec -u 33 nextcloud-app php occ config:system:set overwriteprotocol --value="http"
docker exec -u 33 nextcloud-app php occ config:system:set overwritehost --value="nextcloud.local:8080"

# Verificar
docker exec -u 33 nextcloud-app php occ config:system:get trusted_domains
```

**Probar configuración**

```bash
# Añadir resolución local (opcional, para pruebas)
echo "127.0.0.1 nextcloud.local" | sudo tee -a /etc/hosts

# Probar acceso via proxy
curl http://nextcloud.local:8080

# O directamente por IP
curl http://tu-ip:8080
```

## Gestión de usuario y grupos

https://www.youtube.com/watch?v=mQhbkOKpPTU&t=758s

## Mail

https://www.youtube.com/watch?v=4LjcjbGUFQw


## Error: no puedo acceder a la Nube despues de configurar mi proxy manager

![[nginx_no_llegaa_a_la_app.png]]

Modifique el PASO 1 yendo directamente a la carpeta config dentro de la carpeta de nextcloud y cambie el archivo config.php

```php
<?php
$CONFIG = array (
  'htaccess.RewriteBase' => '/',
  'memcache.local' => '\\OC\\Memcache\\APCu',
  'apps_paths' =>
  array (
    0 =>
    array (
      'path' => '/var/www/html/apps',
      'url' => '/apps',
      'writable' => false,
    ),
    1 =>
    array (
      'path' => '/var/www/html/custom_apps',
      'url' => '/custom_apps',
      'writable' => true,
    ),
  ),
  'upgrade.disable-web' => true,
 'instanceid' => 'ocleeal9lzgy',
  'passwordsalt' => 'ej6MUkyZz70GtWouwOdMbOAAg6wKHo',
  'secret' => 'o41TwCZEQmnS5rrg+uN6gUKJndmKi8E7RlYiCTo88Oq8+2gk',
  'trusted_domains' =>
  array (
    0 => '192.168.0.10:7080', // Cambie porque estaba en el puerto 9090
    1 => 'nextcloud.local',
    2 => 'nextcloud.local:7080', //Esta url la agregue
  ),
  'datadirectory' => '/var/www/html/data',
  'dbtype' => 'pgsql',
  'version' => '27.1.11.3',
  'overwrite.cli.url' => 'http://192.168.0.10:7080', //cambie porque estaba en 9090
  'dbname' => 'nextcloud',
  'dbhost' => 'nextcloud-db',
  'dbport' => '',
  'dbtableprefix' => 'oc_',
  'dbuser' => 'oc_sanmartin-nextcloud',
  'dbpassword' => 'oS3SrOtTkEC4TDl93WLlmfv4mbedal',
  'installed' => true,
  'maintenance' => false,
  'memcache.distributed' => '\\OC\\Memcache\\Redis',
  'memcache.locking' => '\\OC\\Memcache\\Redis',
  'redis' =>
  array (
    'host' => 'nextcloud-redis',
    'password' => '',
    'port' => 6379,
  ),
  'enable_previews' => false,
  'preview_max_x' => 0,
  'preview_max_y' => 0,
  'overwriteprotocol' => 'http',
  'overwritehost' => 'nextcloud.local:7080', //esto antes estaba en nextcloud:9090
);
```

>[!NOTA]
>Me queda para ver en la carpeta de configuracion el archivo .htaccess para que sirve. El apache-pretty-urls.config.php, el reverse-proxy.config.php. Despues hay otros que hacen referencia a un s3, a redis y al smtp que entiendo a donde remiten



# Repasamos

1. Configuracion de la app nextcloud (config del php)

nextcloud/config/config.php

```php
  'trusted_domains' => 
  array (
    0 => '192.168.0.10',
    1 => '181.23.23.111', 
    2 => 'smdev.com.ar',
  ),
  
  'overwritehost' => 'smdev.com.ar',
  'overwriteprotocol' => 'https',
```

2. Configuracion del servidor de host de la aplicacion en el contenedor de nextcloud-app

Configurar usuario y pasword: /etc/apache2/envvars

```bash
# Include generic snippets of statements
IncludeOptional conf-enabled/*.conf

# Include the virtual host configurations:
IncludeOptional sites-enabled/*.conf
ServerName localhost

```

Configurar un host virtual?

```bash
# Acceder al contenedor Nextcloud
docker exec -it nextcloud-container bash

# Ver archivos de configuración de Apache
ls /etc/apache2/sites-enabled/

# Ver contenido del virtual host
cat /etc/apache2/sites-enabled/nextcloud.conf
# o
cat /etc/apache2/sites-enabled/*.conf
```


```bash
<VirtualHost *:80>
    ServerName localhost
    DocumentRoot /var/www/html

    # AGREGAMOS ESTOS Headers para proxy reverso
    RemoteIPHeader X-Real-IP
    RemoteIPTrustedProxy 192.168.0.10
    RemoteIPTrustedProxy 181.46.57.43
</VirtualHost>
```

**Por que agregamos los encabezados en el virtual host ?**

- Nextcloud puede detectar intentos de brute force desde IPs reales
- Los logs de acceso muestran IPs reales de usuarios
- Puedes implementar rate limiting por IP real
- Estadísticas de acceso correctas
- Geolocalización funciona
- Auditoría de seguridad apropiada

En mi NGINX Proxy Host

```bash
# MANTENER esto y AGREGAR:
location / {
    proxy_pass http://192.168.0.10;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Host $host;
    
    # WebSocket para Nextcloud Talk
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    
    # Timeouts
    proxy_connect_timeout 300;
    proxy_send_timeout 300;
    proxy_read_timeout 300;
    
    client_max_body_size 512M;
}
```

**Por que agregamos los encabezados en el proxy host ?**

- En mi NGINX convierte las ip de los usuarios en su ip
- Envia a Nextcloud la ip real de los usuarios

3. Proxy activado en Cloudflare

```bash
### Configuración de Cloudflare

- En Cloudflare, asegúrate de que el proxy esté activado (nube naranja)
- Configura los DNS: `cloud.sanmartinici.com.ar` -> `181.46.57.143`
```


sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
9122/tcp                   ALLOW       Anywhere                   # SSH personalizado
80/tcp                     ALLOW       Anywhere                   # Servidor Web HTTP
443/tcp                    ALLOW       Anywhere                   # Servidor Web HTTPS
8080                       ALLOW       Anywhere
9122/tcp (v6)              ALLOW       Anywhere (v6)              # SSH personalizado
80/tcp (v6)                ALLOW       Anywhere (v6)              # Servidor Web HTTP
443/tcp (v6)               ALLOW       Anywhere (v6)              # Servidor Web HTTPS
8080 (v6)                  ALLOW       Anywhere (v6)


# Repasamos 2

configuracion de nextcloud, puedo modificar el volumen directamente desde el servidor ubicado en srv/nextcloud o acceder al contenedor nextcloud-app

el archivo a modificar es conf/config.php
```php
'trusted_domains' =>
  array (
    0 => '192.168.0.10',
    1 => '181.46.57.143',
    2 => 'cloud.sanmartinici.com.ar',
  ),
  'overwrite.cli.url' => 'http://cloud.sanmartinici.com.ar:8088', //maneja el redirect al usuario
  'overwritehost' => 'cloud.sanmartinici.com.ar:8088', //agregamos el puerto para el redireccionamiento
```

con esta configuración es innecesario aplicar además la configuración en el proxy host

```nginx
location / {
    proxy_set_header Host $host:8088;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Host $host:8088;
    
    # Para forzar el puerto en las redirecciones
    proxy_redirect http://$host/ http://$host:8088/;
    proxy_redirect https://$host/ http://$host:8088/;
}
```

Resolver mi dominio sin necesidad de modificar el host archivo en mi pc clinete admin

Usar la IP directamente con Header Host

```bash
# Desde línea de comandos
curl -H "Host: cloud.sanmartinici.com.ar" http://192.168.0.10:8088
```

O usar herramientas como Postman que permiten setear el Header Host

Opción 2: DNS local en el router (RECOMENDADO)

Muchos routers permiten configurar DNS locales:

1. Accede a tu router (192.168.0.1 o similar)    
2. Busca en **DNS**, **LAN**, o **DHCP**
3. Añade entrada: `cloud.sanmartinici.com.ar → 192.168.0.10`
4. **Todos los dispositivos** en tu red lo resolverán automáticamente

>[!NOTA]
>Probamos esto


---
#### Referencias

