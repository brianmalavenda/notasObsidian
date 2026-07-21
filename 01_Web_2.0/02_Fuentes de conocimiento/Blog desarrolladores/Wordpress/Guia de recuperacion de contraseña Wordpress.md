tipo::
tags::
fecha:: 2026-05-28
links:: [[Wordpress]]
links_externos::

### Paso 1: Ingresar al servidor

Primero, entrá por SSH a tu máquina virtual, a la consola de tu maquina local donde este Wordpress. Si wordpress esta alojado en un contenedor de Docker primero acceder donde corre el servicio de docker.

```
docker ps
```

Busca el **NAMES** o **CONTAINER ID** del contenedor de tu base de datos (por ejemplo, `wordpress-db` o `mariadb-site`).

### Paso 2: Entrar al contenedor de la Base de Datos

Una vez que identificaste el nombre del contenedor de la base de datos, vas a meterte dentro de él ejecutando un shell interactivo:

```
docker exec -it <nombre_contenedor_db> bash
```

_(Si te da error porque no tiene `bash`, probá cambiando `bash` por `sh` al final)._

### Paso 3: Conectarse a MySQL/MariaDB e identificar la BD

Dentro del contenedor, conéctate a la consola de la base de datos usando el usuario `root` (te va a pedir la contraseña que definiste en tu archivo `docker-compose.yml` o en el despliegue inicial):

```bash
mysql -u root -p #la contraseña de la base de datos que debe estar en el yaml
```

Una vez adentro del prompt de MySQL (`mysql>`), listá las bases de datos para asegurarte de cuál es la de WordPress:

```sql
SHOW DATABASES;
```

Seleccioná la base de datos correspondiente (reemplaza `wordpress_db` por el nombre de la tuya):

```sql
USE wordpress_db;
```

### Paso 4: Verificar qué usuarios existen

Para ver qué usuarios se restauraron desde el backup y cuál es el ID del administrador, tirá esta consulta:

```sql
SELECT ID, user_login, user_email FROM wp_users;
```

_(Nota: Si al instalar el WordPress viejo usaste un prefijo de tablas personalizado, la tabla podría llamarse `wp_algo_users` en vez de `wp_users`. Podés verificarlo antes con `SHOW TABLES;`)._

Anotá el `user_login` del administrador que quieras recuperar y fijate su **ID** (generalmente es el `1`).

### Paso 5: Restablecer la contraseña (usando MD5)

WordPress almacena las contraseñas encriptadas. Para setear una nueva de forma manual y que el sistema la reconozca, tenemos que usar la función `MD5()`.

Ejecutá el siguiente comando reemplazando `TuNuevaContrasenaSegura` por la que quieras usar, y el `ID = 1` por el ID del usuario que encontraste en el paso anterior:

```sql
UPDATE wp_users SET user_pass = MD5('TuNuevaContrasenaSegura') WHERE ID = 1;
```

Si todo salió bien, el servidor te va a devolver un mensaje tipo _Query OK, 1 row affected_.

---
#### Referencias

