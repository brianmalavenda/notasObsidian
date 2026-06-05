tipo::
tags::
fecha:: 2026-05-28
links:: [[Wordpress]]
links_externos::

# Diagnóstico de sitios webs lentos

## Objetivo

## El Método de las 4 Capas de Descarte

Cuando un servicio externo falla, el error se suele manifestar en el navegador (Capa de Aplicación), pero la causa raíz puede estar en cualquier eslabón de la cadena. El método consiste en auditar el recorrido del paquete capa por capa:

```
[Internet] ➔ (1. Capa DNS) ➔ (2. Capa de Red/Enrutamiento) ➔ (3. Capa de Transporte/Puertos) ➔ (4. Capa de Aplicación)
```

### Capa 1: Resolución de Nombres (DNS y DDNS)

**Objetivo:** Verificar si el nombre de dominio (`andes.com.ar`) se traduce correctamente a la IP pública actual de la red (WAN).

- **Punto de control:** El contenedor encargado del Dynamic DNS (DDNS) y el panel de Cloudflare.  
- **Herramienta de diagnóstico:** `nslookup`, `dig` o la consulta de logs del contenedor DDNS.
- **Acción metodológica:** 
	1. Validar que el contenedor DDNS no tenga errores de resolución  
	2. Confirmar que la IP que figura en los registros `A` de Cloudflare coincida exactamente con la IP pública real que entrega el ISP en ese momento.
- **Criterio de éxito:** El dominio apunta a la IP correcta. Si el DNS resuelve pero sigue sin haber acceso, se avanza a la siguiente capa.
    

### Capa 2: Enrutamiento e Infraestructura Interna (Network & Gateway)

**Objetivo:** Garantizar que los paquetes de datos puedan viajar entre las distintas subredes/VLANs de la arquitectura local y que tengan camino de retorno a Internet.

- **Punto de control:** Configuración de red de las Máquinas Virtuales (VM) y Contenedores (LXC) en el hipervisor (Proxmox).    
- **Herramienta de diagnóstico:** `ping`, `curl -I` desde el terminal, y la pestaña _Network_ de Proxmox.
- **Acción metodológica:**
    1. **Camino de Ida:** Verificar que el Proxy Inverso (NPM) llegue al contenedor de la aplicación (Nextcloud).
    2. **Camino de Vuelta (El cuello de botella clásico):** Validar que el contenedor de la aplicación tenga configurado el **Gateway correcto** de su propia VLAN. Si el Gateway está vacío o apunta a otra VLAN, el contenedor recibe el paquete pero no sabe cómo responderle a una IP externa de Internet.
- **Criterio de éxito:** El LXC/Contenedor tiene salida a internet y comunicación Inter-VLAN fluida.

### Capa 3: Permisión y Transporte (Puertos y Firewalls)

**Objetivo:** Asegurar que los "puentes" estén levantados tanto en la frontera de la red (Router) como en los sistemas operativos internos (Firewalls).

- **Punto de control:** Tabla de _Virtual Servers_ (Port Forwarding) del router principal y firewalls locales (`ufw`, `iptables`).
- **Herramienta de diagnóstico:** Escaneo de puertos externo o pruebas de acceso por IP local.
- **Acción metodológica:**
    1. Confirmar que el Router redirija de forma estricta los puertos `80` (HTTP) y `443` (HTTPS) hacia la IP privada exacta del Proxy Inverso (NPM), no de la aplicación final.
    2. Comprobar que el firewall del host receptivo (`sudo ufw status`) no esté denegando el tráfico procedente de subredes externas.
- **Criterio de éxito:** Al ingresar la IP local del proxy desde el navegador en la red interna, este responde (ej. mostrando la página de _"Congratulations"_ de Nginx).

### Capa 4: Capa de Aplicación (Protocolos, Cabeceras y SSL)

**Objetivo:** Una vez que el paquete llega físicamente al software, sincronizar los "idiomas" (esquemas de cifrado y variables de confianza) entre el Proxy y la Aplicación.

- **Punto de control:** Interfaz de Nginx Proxy Manager y archivo de configuración del servicio (ej. `config.php` de Nextcloud).
- **Códigos de error de referencia (La guía definitiva):**
    - **Error 504 (Gateway Time-out):** El proxy intenta hablarle al contenedor pero este no responde. Suele ser un error de _esquema_ (Nginx intentando hablar HTTPS a un puerto HTTP) o un Gateway roto en el contenedor.
    - **Error 400 (Bad Request):** El paquete _llegó_ a la aplicación, pero la aplicación lo rechaza porque no confía en el origen.
- **Acción metodológica:**
    1. **Ajustar el Esquema (Forward Scheme):** Identificar si el contenedor interno fue compilado para recibir HTTP o HTTPS nativo en su puerto expuesto (como descubrimos con Nextcloud, que exigía `https` en el puerto `8008` debido a su certificado autofirmado interno).
    2. **Declaración de Confianza:** Configurar las variables `trusted_proxies` y `trusted_domains` en la aplicación para autorizar explícitamente la IP del Proxy Inverso (`10.0.37.10`) a entregar peticiones en nombre de usuarios externos. En el caso de la configuracion de una app php esto se modifica en el ```var/wwww/html/wp-config.phph```

```php
'trusted_domains' =>
 array (
  0 => '10.0.37.13:8008', 
  1 => 'andes.sanmartin.local', 
  2 => 'andes.sanmartinici.com.ar', 
), 
'trusted_proxies' => 
array (
  0 => '10.0.37.10', // IP exacta de tu LXC Nginx Proxy Manager 
  1 => '172.16.0.0/12', // Rango interno de Docker 
), 
'forwarded_for_headers' => 
array( 
  0 => 'HTTP_X_FORWARDED_FOR', 
),
'dbtype' => 'mysql', 
'version' => '33.0.3.2', 
'overwrite.cli.url' => 'https://andes.sanmartinici.com.ar', 
'overwriteprotocol' => 'https', 
'overwritehost' => 'andes.sanmartinici.com.ar',
```


---
#### Referencias

