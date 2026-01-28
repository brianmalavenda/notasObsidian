tipo::
tags::
fecha:: 2025-12-12
links:: [[01_Wordpress]] [[03_Plugins]]
links_externos:: [Main WP Video Review](https://www.youtube.com/watch?v=OP_vwW3AOpo)  [MainWP en WordPress.org](https://es.wordpress.org/plugins/mainwp/)


**MainWP** es un ecosistema de código abierto (GPLv3) que funciona bajo el modelo "Hub & Spoke".

- **MainWP Dashboard (El Hub):** Es un WordPress independiente que instalas en tu servidor (en su propio puerto/sitio). Desde aquí controlas todo.
- **MainWP Child (El Spoke):** Un plugin ligero que instalas en cada uno de tus 5 sitios WordPress.
- **Extensibilidad:** Su potencia real está en las **extensiones** (muchas gratuitas, otras premium).
    
#### **¿Qué puedes hacer con MainWP (y extensiones gratuitas)?**

1. **Gestión Centralizada:** Actualizar núcleo, plugins y temas en todos los sitios a la vez o de forma selectiva.
2. **Monitorización:** Ver el estado de salud, errores, disponibilidad y seguridad de todos los sitios desde un solo lugar.
3. **Gestión de Usuarios:** Administrar usuarios en los sitios hijos desde el panel central.    
4. **Publicación Centralizada:** Crear y publicar contenido en varios sitios a la vez.
5. **Respaldos (Backups):** Con extensiones gratuitas como `BackupWP` (o integración con UpdraftPlus), puedes programar y almacenar backups en tu servidor.
6. **Seguridad:** Escaneo básico de plugins vulnerables, gestión de inicios de sesión, etc.
7. **Clonación/Migración:** Mover sitios entre servidores directamente desde el panel.
#### **Cómo implementarlo en tu entorno:**

1. Crea un nuevo sitio WordPress (ej: `dashboard.milab.local:8000`). Este será tu **Panel de Control Maestro (Hub)**.
2. Instala el plugin **MainWP Dashboard** en ese sitio.
3. En cada uno de tus **5 sitios existentes**, instala el plugin **MainWP Child**.
4. Desde el Dashboard, agregas cada sitio hijo usando una clave de conexión. MainWP se comunicará con ellos a través de la API de WordPress.
5. Instala las **extensiones gratuitas** desde el Dashboard según tus necesidades (Monitor, Backups, Actualizaciones, etc.).


---
#### Referencias

