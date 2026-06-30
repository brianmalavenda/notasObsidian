tipo::
tags:: #redes 
fecha:: 2026-06-12
links::
links_externos::

##  Objetivo
Establecer un protocolo de descarte estandarizado y secuencial para identificar y resolver fallos de acceso a servicios autohospedados (*Self-Hosted*) expuestos a través de un Proxy Inverso en entornos de red segmentados de infraestructura doméstica avanzada.

##  El Método Flow-Top (Enfoque Bottom-Up)

Cuando un servicio externo falla, el error se manifiesta en la interfaz del cliente (Navegador), pero la causa raíz suele estar escondida en los cimientos de la infraestructura. Este método audita el recorrido del paquete de datos **desde afuera hacia adentro**, aislando variables capa por capa.

```

[Internet] └── 🌐 1. Capa DNS (Traducción de nombres) └── 🛣️ 2. Capa de Red e Infraestructura (Gateways & Routing) └── 🔌 3. Capa de Transporte y Permisión (Puertos & Firewalls) └── 🎛️ 4. Capa de Aplicación (Protocolos, Cabeceras & SSL)

````

---

### Capa 1: Resolución de Nombres (DNS y DDNS)
**Objetivo:** Verificar si el nombre de dominio público (`andes.sanmartinici.com.ar`) se traduce correctamente a la IP pública actual asignada por el ISP (WAN).

* **Puntos de control:** Contenedores de [[Dynamic DNS (DDNS)]] y panel de Cloudflare.
* **Herramientas de diagnóstico:** `nslookup`, `dig`, o inspección directa de los logs del contenedor DDNS.

> [!WARNING] ¡Ojo con el bucle IPv6 local!
> Un error común en entornos virtualizados es que el motor de Docker intente heredar la dirección de Loopback IPv6 del host (`::1`). Si esto pasa, el contenedor DDNS quedará "ciego" lanzando errores como `curl: (6) Could not resolve host`. Forzar el uso de DNS IPv4 estáticos (como el AdGuard local o `1.1.1.1`) resuelve este cuello de botella.

* **Criterio de éxito:** El dominio resuelve limpia y públicamente hacia la IP de tu WAN actual. Si los registros `A` coinciden pero sigue sin haber acceso, pasamos a la Capa 2.

---

### Capa 2: Enrutamiento e Infraestructura Interna (Network & Gateway)
**Objetivo:** Garantizar que los paquetes de datos puedan viajar entre las distintas subredes/[[VLANs]] de la arquitectura local y, fundamentalmente, que tengan el camino de regreso abierto hacia Internet.

* **Puntos de control:** Configuración de red de las VMs y Contenedores (LXC) en el hipervisor ([[Proxmox]]).
* **Herramientas de diagnóstico:** `ping`, `curl -I` desde consolas internas.

> [!DANGER] El Síndrome del "Camino de Ida sin Vuelta"
> En redes segmentadas (ej: Proxy en VLAN_PROD `10.0.37.X` y Gestión en VLAN_ADMIN `10.0.17.X`), un contenedor puede recibir peticiones externas pero ser incapaz de responderlas si su **Default Gateway** está vacío o apunta a la subred incorrecta. 
> * **Regla de oro:** El LXC/VM debe apuntar estrictamente a la IP de la interfaz del router que gestiona *su propia VLAN* (ej: `10.0.37.1`).

* **Criterio de éxito:** Comunicación Inter-VLAN fluida y capacidad del LXC para resolver y salir a internet de forma autónoma.

---

### Capa 3: Permisión y Transporte (Puertos y Firewalls)
**Objetivo:** Asegurar que los puertos de enlace perimetrales estén abiertos en el router y que los firewalls internos de los sistemas operativos no estén bloqueando el tráfico legítimo.

* **Puntos de control:** Tabla de *Virtual Servers* (Port Forwarding) en el Router principal y firewalls locales (`ufw`, `iptables`).
* **Herramientas de diagnóstico:** Escaneo de puertos externo (`nmap`) o peticiones HTTP locales por IP.

> [!INFO] Nota de Arquitectura sobre Seguridad (DMZ)
> Por diseño de seguridad estricto, el Port Forwarding del router (puertos `80` y `443`) debe apuntar **única y exclusivamente** a la IP del LXC que aloja el Proxy Inverso (`10.0.37.10`). Nunca se debe exponer la IP del contenedor de la aplicación final de forma directa a internet.

* **Criterio de éxito:** Al ingresar la IP privada del proxy desde el navegador en la red interna, este responde (ej: mostrando la página por defecto de Nginx).

---

### Capa 4: Capa de Aplicación (Protocolos, Cabeceras y SSL)
**Objetivo:** Sincronizar el "idioma" de comunicación, los esquemas de cifrado y las variables de confianza entre el Proxy Inverso y el servidor de la aplicación final.

* **Puntos de control:** Interfaz de [[Nginx Proxy Manager]] (NPM) y archivos de configuración del servicio (ej: `config.php` de Nextcloud).

#### 🗂️ Diccionario de Errores de Aplicación (Troubleshooting Rápido)

| Código de Error | Significado Técnico | Causa Común en Homelabs |
| :--- | :--- | :--- |
| ==**504 Gateway Time-out**== | El proxy no recibe respuesta del contenedor destino. | • Error de *esquema* de red.<br>• Gateway roto en el contenedor de la App.<br>• El contenedor destino está apagado. |
| ==**400 Bad Request**== | El paquete llegó a la App, pero esta lo rechaza explícitamente. | • La App no confía en la IP del Proxy.<br>• Desajuste de cabeceras (`X-Forwarded-For`).<br>• Conflicto con proxies externos (Cloudflare). |

> [!TIP] Particularidad de Esquemas HTTPS Internos
> Aunque la regla general dicta que NPM debe hablarle a los contenedores por HTTP plano en su puerto expuesto, ciertas imágenes avanzadas (como las de *Linuxserver.io*) vienen blindadas con **certificados SSL autofirmados de fábrica**. En estos casos específicos, configurar el *Forward Scheme* en **HTTPS** dentro de NPM es mandatorio para evitar que el servidor interno rechace la conexión con un error 400.

#### 📝 Configuración de Confianza Requerida (`config.php`)
Cuando la aplicación vive detrás de un proxy en otra VLAN, es obligatorio indicarle que confíe en las cabeceras redirigidas:
```php
  'trusted_proxies' => array(
    0 => '10.0.37.10', // IP exacta de tu LXC Nginx Proxy Manager
    1 => '172.16.0.0/12',  // Rango interno de Docker (Bridge)
  ),
  'forwarded_for_headers' => array(
    0 => 'HTTP_X_FORWARDED_FOR',
  ),
````


---
#### Referencias

