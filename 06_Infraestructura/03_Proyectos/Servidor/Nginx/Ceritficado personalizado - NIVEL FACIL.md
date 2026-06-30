tipo::
tags:: #redes 
fecha:: 2026-06-12
links::
links_externos::

Esta guía detalla el procedimiento paso a paso para exponer servicios autohospedados mediante subdominios personalizados bajo un dominio local (`.local`), centralizando el tráfico a través de un proxy inverso y eliminando las advertencias de seguridad del navegador mediante una Autoridad de Certificación (CA) propia.
## Arquitectura del Proyecto

El flujo del tráfico local se estructurará de la siguiente manera:
1. **Usuario** ingresa a `https://servicio.midominio.local` desde su navegador.
2. **DNS Local** intercepta la solicitud y la resuelve hacia la IP del Proxy Inverso.
3. **Nginx Proxy Manager (NPM)** recibe la conexión cifrada, valida el certificado comodín y redirige internamente el tráfico al servicio final (`IP:Puerto`).

---

## Requisitos Previos

- Servidor DNS local en funcionamiento (AdGuard Home, Pi-hole o similar).
- Nginx Proxy Manager corriendo en la red (ej. en un contenedor LXC de Proxmox).
- Una máquina de administración con **Windows** y privilegios de Administrador.

## Paso 1: Configurar la Resolución DNS Local

Para que los nombres de dominio apunten al proxy inverso, debemos crear un registro en nuestro servidor DNS de cabecera.

### Opción A: Configuración en AdGuard Home
1. Inicia sesión en la interfaz web de **AdGuard Home**.
2. Ve a **Filtros** > **Reescrituras de DNS** (DNS Rewrites).
3. Haz clic en **Añadir reescritura de DNS**.
4. En **Nombre de dominio**, ingresa tu dominio comodín o específico:
   - Específico: `portainer.sanmartin.local`
   - Comodín (Recomendado): `*.sanmartin.local`
5. En **Dirección IP**, introduce la **IP asignada a tu contenedor de Nginx Proxy Manager**.
6. Haz clic en **Guardar**.

> [!NOTE]
> El uso de un comodín (`*.midominio.local`) permite que cualquier subdominio nuevo que crees en el futuro sea redirigido automáticamente a NPM sin necesidad de volver a tocar la configuración de AdGuard.

---

## Paso 2: Generar la CA Local y el Certificado Comodín (Windows)

Como los dominios internos `.local` no pueden ser validados de forma pública por entidades como *Let's Encrypt*, utilizaremos **`mkcert`** para convertir nuestra máquina de administración en una Entidad de Certificación confiable.

### 2.1 Instalación de mkcert
1. En tu máquina Windows, haz clic derecho en el botón de Inicio y abre **PowerShell (Administrador)** o **Terminal (Administrador)**.
2. Instala la herramienta utilizando el gestor de paquetes de Windows ejecutando:

```powershell
   winget install mkcert
```

3. Reinicia la terminal para asegurar que el comando sea reconocido por el sistema.

### 2.2 Creación de la Entidad de Certificación (CA)

Ejecuta el siguiente comando para generar e instalar la raíz de confianza en el almacén de certificados de Windows:

PowerShell

```
mkcert -install
```

> [!IMPORTANT] Aparecerá una ventana emergente de seguridad de Windows solicitando confirmación para instalar el certificado raíz. Haz clic en **Sí**. A partir de este momento, tu navegador (Chrome, Edge, Firefox) confiará en los certificados emitidos por esta herramienta.

### 2.3 Generación del Certificado Comodín SSL/TLS

1. Crea una carpeta en tu almacenamiento local donde desees guardar los archivos (ej. `C:\Certificados`).
2. Navega hasta esa carpeta desde la terminal de PowerShell:
    
    ```poweshell
    cd C:\Certificados
    ```
    
3. Genera el certificado comodín y el del dominio base ejecutando el siguiente comando:    
   
    ```
    mkcert "*.sanmartin.local" sanmartin.local
    ```
    
4. Este comando generará dos archivos críticos en el directorio:    
    - `_wildcard.sanmartin.local+1.pem` (Este archivo corresponde al **Certificado público**).
    - `_wildcard.sanmartin.local+1-key.pem` (Este archivo corresponde a la **Clave Privada**).
## Paso 3: Importar el Certificado en Nginx Proxy Manager

Con los archivos PEM generados en nuestra máquina Windows, procederemos a cargarlos en el panel de NPM.

1. Accede al panel web de administración de **Nginx Proxy Manager**.
2. Dirígete a la pestaña **SSL Certificates** en el menú superior.
3. Haz clic en el botón **Add SSL Certificate** y selecciona **Custom**.
4. Completa los campos solicitados:
    - **Name**: Asigna un nombre descriptivo (ej. `Comodín San Martín (mkcert)`).
    - **Certificate Key**: Haz clic y sube el archivo de clave privada (`_wildcard.sanmartin.local+1-key.pem`).
    - **Certificate**: Haz clic y sube el archivo de certificado (`_wildcard.sanmartin.local+1.pem`).
5. Presiona **Save**.

## Paso 4: Crear y Configurar el Proxy Host

Por último, asociaremos el subdominio específico con el servicio interno de nuestro homelab. Tomaremos como ejemplo la redirección a **Portainer**.

1. En NPM, ve a la pestaña **Hosts** > **Proxy Hosts** y haz clic en **Add Proxy Host**.
2. En la pestaña **Details**, configura los siguientes parámetros:
    - **Domain Names**: Escribe `portainer.sanmartin.local` y presiona `Enter`.
    - **Scheme**: Selecciona `https` _(Nota: Portainer utiliza HTTPS nativo en su puerto por defecto. Si tu servicio usa HTTP estándar, selecciona http)_.
    - **Forward Hostname / IP**: La dirección IP real del servicio (ej. `10.0.37.13`).
    - **Forward Port**: El puerto de escucha del servicio (ej. `9443`).
    - **Block Common Exploits**: Activado (Añade una capa básica de seguridad).
    - **Websockets Support**: **Activado** _(Esencial para que funcionen las terminales interactivas VNC/Docker en aplicaciones como Portainer o Proxmox)_.
3. Dirígete a la pestaña **SSL** del mismo formulario:
    - **SSL Certificate**: Selecciona del menú desplegable el certificado personalizado que subiste en el Paso 3 (`Comodín San Martín (mkcert)`).
    - **Force SSL**: Activado (Redirige automáticamente el tráfico HTTP no cifrado hacia el puerto HTTPS seguro).
    - **HTTP/2 Support**: Activado (Optimiza el rendimiento de la conexión).
4. Haz clic en **Save**.

## 🧪 Paso 5: Verificación

1. Abre una ventana de incógnito en tu navegador web desde la máquina de administración.
2. Accede a la URL configurada: `https://portainer.sanmartin.local`.
3. Verifica que el sitio cargue correctamente y comprueba que en la barra de direcciones aparezca el ícono del **candado cerrado de forma segura**, detallando que el certificado es válido y confiable.

> [!TIP] **¿Qué pasa con los otros dispositivos de la red?** Al haber ejecutado `mkcert -install`, el certificado raíz solo es de confianza en la máquina de administración Windows. Si deseas que otros equipos o teléfonos de tu red accedan sin advertencias de seguridad, debes buscar el archivo `rootCA.pem` generado por mkcert (puedes hallar su ubicación ejecutando `mkcert -CAROOT` en la terminal) e instalar ese archivo específico en el almacén de "Entidades de certificación raíz de confianza" de cada dispositivo adicional.