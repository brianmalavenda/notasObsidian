tipo::
tags::
fecha:: 2025-12-11
links::
links_externos::

### Ubicación de los datos de tu sitio

Según tu archivo `docker-compose.yml`, los datos se almacenan en dos lugares:

1. **Base de datos (MySQL)**:
    
    yaml
    
    volumes:
      - db_data:/var/lib/mysql
    
    - **Tipo**: Volumen de Docker con nombre (`db_data`).
        
    - **Ubicación física**: Gestionada por Docker (no visible directamente en tu sistema de archivos a menos que uses comandos específicos).
        
    - **Contiene**: Todas las tablas de WordPress, usuarios, configuraciones, publicaciones, etc.
        
2. **Archivos de WordPress (código y plugins)**:
    
    yaml
    
    volumes:
      - './:/var/www/html'
    
    - **Tipo**: Volumen montado desde el host (tu máquina).
        
    - **Ubicación física**: El directorio actual (`.`) donde se encuentra tu archivo `docker-compose.yml`.
        
    - **Contiene**: Todos los archivos PHP de WordPress, temas, plugins y las subcarpetas `wp-content/uploads`, `wp-content/plugins`, `wp-content/themes`.
        

### 🧹 **Procedimiento para borrar y empezar de nuevo**

Sigue estos pasos **en el orden exacto** para asegurar una limpieza completa:

**Paso 1: Detener y eliminar los contenedores**  
Ejecuta desde el directorio de tu `docker-compose.yml`:

bash

docker-compose down

**Paso 2: Eliminar el volumen de la base de datos**  
Este es el paso más importante, ya que elimina la base de datos corrupta con las URL viejas.

bash

docker volume rm $(docker volume ls -q --filter name=db_data)

Si obtienes un error porque el volumen no existe, simplemente continúa con el siguiente paso. Docker Compose lo recreará.

**Paso 3: Limpiar los archivos de WordPress del host**  
En el mismo directorio donde está tu `docker-compose.yml`, ejecuta:

bash

# Elimina todo EXCEPTUANDO el archivo docker-compose.yml y cualquier otro archivo de configuración que quieras conservar
rm -rf wp-admin wp-content wp-includes wp-*.php index.php license.txt readme.html

**Paso 4: Verificar la estructura limpia**  
Tu directorio debería quedar similar a esto (solo con los archivos esenciales de Docker):

text

tu_directorio/
├── docker-compose.yml
├── uploads.ini
└── (ninguna carpeta o archivo de WordPress)

**Paso 5: Levantar un WordPress limpio**

bash

docker-compose up -d

Ahora tendrás una instalación **completamente nueva y vacía** de WordPress accesible en `http://tu-ip:8000`.


---
#### Referencias

