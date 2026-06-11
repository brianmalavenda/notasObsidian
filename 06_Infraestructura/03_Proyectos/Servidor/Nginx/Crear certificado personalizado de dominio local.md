tipo::
tags::
fecha:: 2026-06-04
links::
links_externos::

**Objetivo:** Reemplazar el acceso por IP (`[https://10.10.10.10:8211](https://10.10.10.10:8211)`) por un dominio local seguro (`[https://bkp.local](https://bkp.local)`) utilizando Nginx como Reverse Proxy y un certificado SSL custom válido para navegadores modernos.
### Paso 1: Generar el Certificado SSL Custom (Windows)

Abre la terminal de Windows (CMD o Git Bash) donde tengas configurado OpenSSL y ejecuta el siguiente comando modernizado. Este incluye la extensión `subjectAltName` (SAN) que exigen los navegadores actuales para no dar error:

```
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout bkp.local.key -out bkp.local.crt -subj "/CN=bkp.local" -addext "subjectAltName=DNS:bkp.local"
```

- **Resultado:** Obtendrás inmediatamente dos archivos en el directorio donde estás parado: `bkp.local.key` (clave privada) y `bkp.local.crt` (certificado).

![[credenciales_nginx.png]]
### Paso 2: Configurar Nginx como Reverse Proxy (Servidor Linux)

1. Sube los dos archivos generados al servidor de Nginx, idealmente en la ruta `/etc/nginx/ssl/`.
	1. Crea o edita el archivo de configuración de tu sitio (por ejemplo, `/etc/nginx/sites-available/bkp.local`):
    

```
server {
    listen 80;
    server_name bkp.local;
    # Redirigir todo el tráfico HTTP a HTTPS automáticamente
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name bkp.local;

    # Rutas a los certificados generados con OpenSSL
    ssl_certificate /etc/nginx/ssl/bkp.local.crt;
    ssl_certificate_key /etc/nginx/ssl/bkp.local.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    location / {
        # IP y puerto real de tu Proxmox Backup Server
        proxy_pass https://10.10.10.10:8211; 
        
        # Cabeceras requeridas para el correcto funcionamiento de la interfaz de Proxmox
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # Soporte obligatorio para WebSockets (Consola y streaming de logs en tiempo real)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        # Desactivar límite de tamaño para permitir la subida de backups pesados sin error
        client_max_body_size 0;
    }
}
```

3. Habilita el sitio y reinicia el servicio:

```
ln -s /etc/nginx/sites-available/bkp.local /etc/nginx/sites-enabled/
nginx -t && systemctl restart nginx
```

### Paso 3: Resolución del Nombre Local (`bkp.local`)

Debes indicarle a tu red o a tu computadora hacia dónde apuntar el dominio `bkp.local`.

- **Opción A (Para toda tu red - Recomendado):** Ve a tu servidor DNS local (Pi-hole, AdGuard Home, o el DNS de tu router/firewall) y crea una entrada DNS estática tipo **A** que apunte `bkp.local` hacia la **IP de tu servidor Nginx**.
    
- **Opción B (Solo para tu PC Windows):** Abre el Bloc de notas **como Administrador**, edita el archivo `C:\Windows\System32\drivers\etc\hosts` y añade al final la IP de tu Nginx junto al dominio:
    
    ```
    10.10.10.X   bkp.local
    ```

### Paso 4: Confianza del Certificado en Windows (Candado Seguro)
Para eliminar la advertencia de "Sitio no seguro" en tus navegadores, debes hacer que tu sistema operativo confíe en el certificado que acabas de crear:

1. En tu teclado presiona `Win + R`, escribe `certlm.msc` y presiona Enter (se abrirá el Gestor de Certificados del Equipo Local).
2. En el árbol de la izquierda, despliega **Entidades de certificación de raíz de confianza** y haz clic en la carpeta **Certificados**.
3. Haz clic derecho sobre esa carpeta -> **Todas las tareas** -> **Importar**.
4. Selecciona el archivo `bkp.local.crt` que generaste en el Paso 1 y finaliza el asistente.

Cierra por completo tu navegador, vuélvelo a abrir y accede mediante `[https://bkp.local]`


---
#### Referencias

