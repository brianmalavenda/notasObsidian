tipo:: #servidor/web 
tags::
fecha:: 2025-12-05
links::
links_externos::

## 🚀 Instalación de Portainer (Recomendada con OMV Compose)

Sigue estos pasos a través de la interfaz web de OMV:

### 1. Activar Docker y Portainer en OMV-Extras

1. Ve a **Sistema** (System) > **OMV-Extras**.
    
2. Asegúrate de que el **Repositorio de Docker** esté **Activado** (Enabled).
    
3. Ve a **Servicios** (Services) > **Compose** > **Configuración** (Settings).
    
4. Asegúrate de que las rutas a los archivos de configuración y datos sean correctas. Si no has configurado esto, OMV te guiará.
    

### 2. Crear y Desplegar el Stack de Portainer

Portainer se instalará como un "Stack" (colección de contenedores) usando Docker Compose. OMV facilita esto al proporcionar una plantilla:

1. Ve a **Servicios** (Services) > **Compose** > **Archivos** (Files).
    
2. Haz clic en **Añadir desde Ejemplo** (Add from example).
    
3. En la lista desplegable de **Ejemplo** (Example), selecciona **`portainer`**.
    
4. Asigna un **Nombre** (Name), por ejemplo, `portainer`.
    
5. Haz clic en **Guardar** (Save).
    

### 3. Iniciar el Contenedor de Portainer

1. En la misma sección **Archivos** (Files), verás el nuevo archivo `portainer` en la lista.
    
2. Selecciónalo y haz clic en el botón **Subir** (el icono de la flecha hacia arriba **⬆️**).
    
3. OMV descargará la imagen de Portainer y la ejecutará, creando el volumen persistente para sus datos.
    

### 4. Acceder a Portainer por Primera Vez

1. Una vez que el despliegue esté completo, abre tu navegador.
    
2. Accede a la IP de tu OMV con el puerto `9000` o `9443` (para HTTPS, que es recomendado).
    

> **URL de Acceso (OMV_IP_):** `http://<IP_de_tu_OMV>:9000`

3. Se te pedirá que crees un **usuario `admin` y una contraseña segura**. Hazlo inmediatamente.
    
4. Selecciona la opción **"Get Started"** (Comenzar) y elige la opción **"Local"** (para gestionar el Docker de tu OMV).
    

---

## 💻 Alternativa por Línea de Comandos (SSH)

Si prefieres usar la terminal (SSH), puedes ejecutar el comando `docker run` directamente para Portainer Community Edition (CE).

1. **Conéctate a tu OMV por SSH.**
    
2. **Crea el volumen persistente** para los datos de Portainer:
    
    Bash
    
    ```
    docker volume create portainer_data
    ```
    
3. **Ejecuta el contenedor de Portainer:**
    
    Bash
    
    ```
    docker run -d \
      -p 8000:8000 \
      -p 9443:9443 \
      --name portainer \
      --restart=always \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v portainer_data:/data \
      portainer/portainer-ce:latest
    ```
    

Este comando:

- `--name portainer`: Nombra el contenedor.
    
- `-p 9443:9443`: Mapea el puerto HTTPS 9443 del contenedor al puerto 9443 del host (OMV).
    
- `-v /var/run/docker.sock:/var/run/docker.sock`: **Esencial** para que Portainer pueda comunicarse con el motor de Docker de tu OMV.
    

Una vez finalizado, accede a la URL `https://<IP_de_tu_OMV>:9443` (o por el 9000 si lo cambias).



![[servidor sm901 - portainer ambientes.png]]

![[servidor sm901 - portainer dashboard.png]]


---
#### Referencias

