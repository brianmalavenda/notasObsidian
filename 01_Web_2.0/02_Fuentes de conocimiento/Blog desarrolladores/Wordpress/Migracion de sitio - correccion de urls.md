tipo::
tags::
fecha:: 2026-06-05
links::
links_externos::


### 1. Corregir las URLs en la Base de Datos de WordPress

WordPress guarda a fuego la URL del sitio en su configuración. Si en tu entorno local usabas `http://` o un dominio interno, tenés que actualizarlo a tu nuevo dominio con `https://`.
#### Opción A: Hardcodeo temporal en `wp-config.php`

Para recuperar el control rápido de la web, entrá a los archivos de tu WordPress, editá el archivo `wp-config.php` y agregá estas dos líneas arriba de todo (justo después de `<?php`):

```php
define('WP_HOME', 'https://tu-subdominio.tudominio.com');
define('WP_SITEURL', 'https://tu-subdominio.tudominio.com');
```

_(Cambiá `https://tu-subdominio.tudominio.com` por tu URL real pública)._
#### Opción B: Base de datos (Recomendado a largo plazo)

Si tenés acceso a la base de datos (vía phpMyAdmin o Adminer), entrá a la tabla **`wp_options`** y modificá los campos:

- `siteurl`    
- `home` Asegurate de que ambos arranquen con **`https://`** y tengan el dominio de Cloudflare.
### 2. Configurar el modo SSL en Cloudflare

Este es el error más común al meter a Cloudflare en el medio con Nginx Proxy Manager.

1. Entrá a tu panel de **Cloudflare**.
2. Ir a **SSL/TLS** -> **Overview**.
3. Cambiá el modo de cifrado a **Full (Strict)**.
    
>[!WARNING] **Por qué importa:** Si lo tenés en _Flexible_, Cloudflare se conecta a tu Nginx por HTTP (puerto 80), pero vos le pusiste un certificado Let's Encrypt en tu Nginx y WordPress se confunde, generando bucles de redirección o bloqueos de protocolo como los que se ven en tu captura. Al ponerlo en **Full (Strict)**, todo el viaje va cifrado de punta a punta.

### 3. Forzar HTTPS en el Proxy Inverso (Nginx)

En tu panel de Nginx Proxy Manager, editá el Proxy Host de este dominio y asegurate de tener activadas las siguientes opciones en la pestaña **SSL**:

- **Force SSL** (Activado)
- **HTTP/2 Support** (Activado)
- **HSTS Enabled** (Opcional, pero recomendado)
    
>[!NOTE] Además, si el contenedor de WordPress corre originalmente en HTTP internamente, recordá que en la pestaña _Details_ de NPM, el **Forward Scheme** debe ser `http` y apuntar a la IP local y puerto de tu contenedor. El certificado SSL lo maneja NPM hacia afuera.

### 4. Ajustar el Reverse Proxy en WordPress

A veces WordPress no se da cuenta de que está detrás de un proxy inverso y rompe los enlaces SSL. Si después de los pasos anteriores las imágenes siguen fallando, agregá este bloque en tu `wp-config.php` (justo antes de la línea que dice `/* That's all, stop editing! Happy publishing. */`):

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
    $_SERVER['HTTPS'] = 'on';
}
```

Esto le avisa a WordPress: _"Che, NPM te está mandando la petición limpia, pero afuera estamos usando HTTPS, así que generá todos los links con https://"_.
### 5. Limpieza de URLs viejas (Opcional pero necesario)

Aunque hagas lo anterior, las imágenes que ya tenías subidas en tus posts locales pueden haber quedado guardadas en la base de datos con la IP vieja o el dominio local.

Una vez que puedas entrar al panel de administración (`/wp-admin`):
1. Instalá el plugin **Better Search Replace**.
2. Buscá tu URL vieja (ej: `http://192.168.1.X` o tu dominio local de Adguard).
3. Reemplazala por tu nueva URL pública (`https://tu-subdominio.tudominio.com`).
4. Seleccioná todas las tablas y ejecutá el reemplazo (primero podés hacer un "simulacro" desmarcando la casilla de _Run as dry run_).

---
#### Referencias

