tipo::
tags::
fecha:: 2026-05-14
links::
links_externos::

Esta guía asume que tienes acceso al host de **Proxmox** y que el servicio de Nginx se gestiona a través de una base de datos relacional ligera.

### 1. Acceso al entorno

Desde la shell de Proxmox, entra al contenedor:

Bash

```
pct enter [ID_CONTENEDOR]
```

### 2. Preparación de herramientas

Si el contenedor es una instalación mínima, es probable que no tenga el cliente de SQLite. Instálalo para poder manipular los registros:

Bash

```
apt update && apt install sqlite3 -y
```

### 3. Localización y Apertura de la BD

En Nginx Proxy Manager, la base de datos suele residir en `/data/database.sqlite`. Para abrirla, ejecuta:

Bash

```
sqlite3 /data/database.sqlite
```

### 4. Procedimiento de Reset por SQL

Una vez dentro del prompt de SQLite (`sqlite>`), utilizaremos comandos para "marcar como borrado" al usuario administrativo. Esto engaña al sistema para que, al reiniciar, no encuentre un administrador configurado y habilite el de fábrica.

- **Paso A: Verificar usuarios (Opcional)**
    
    SQL
    
    ```
    SELECT id, email, is_deleted FROM user;
    ```
    
- **Paso B: Ejecutar el Reset**
    
    SQL
    
    ```
    UPDATE user SET is_deleted=1 WHERE id=1;
    ```
    
- **Paso C: Salir**
    
    SQL
    
    ```
    .exit
    ```
    

### 5. Reinicio y Acceso de Emergencia

Para que los cambios en la base de datos se reflejen en la aplicación web, reinicia el contenedor:

Bash

```
# Fuera del contenedor, en la shell de Proxmox
pct restart [ID_CONTENEDOR]
```

Ahora podrás acceder a la interfaz web con las **credenciales por defecto**:

- **Email:** `admin@example.com`
    
- **Password:** `changeme`
    

---

### Notas de seguridad

> [!IMPORTANT] Inmediatamente después de entrar, el sistema te pedirá configurar un nuevo nombre, email y contraseña. Asegúrate de guardar estos nuevos datos en un gestor de contraseñas para evitar tener que manipular la DB nuevamente.

---
#### Referencias


