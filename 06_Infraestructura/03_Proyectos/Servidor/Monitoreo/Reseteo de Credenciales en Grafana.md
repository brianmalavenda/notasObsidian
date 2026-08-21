tipo:: #servidor 
tags:: #monitoreo
fecha:: 2026-08-13
links:: 
links_externos::  **`grafana-offline-admin-reset`** en Gemini

En infraestructuras donde los servicios corren bajo contenedores Docker en segmentos de red aislados o sin salida a Internet (ambientes air-gapped o VPCs restrictivas), la pérdida o desconfiguración de las credenciales de administración de Grafana puede presentar un desafío particular.

Este documento detalla el procedimiento técnico para recuperar el control total del usuario administrador (`admin`) en una instancia de Grafana basada en la base de datos embebida SQLite, solucionando problemas de sobreescritura de usuario, fallos de conectividad hacia repositorios o imágenes externas, y asegurando la integridad de los datos existentes (_dashboards_, _datasources_, _alerting_, etc.).

---

## 1. Diagnóstico y Raíz del Problema

Cuando el comando estándar de la CLI de Grafana (`grafana cli reset-admin-password`) no permite el ingreso a la plataforma, generalmente se debe a dos causales:

1. **Alteración del `login` primario (ID = 1):** La CLI de Grafana intenta restablecer la clave sobre la cuenta registrada con el nombre predeterminado `admin` o la asociada al ID de usuario principal. Si el nombre de usuario (`login`) fue modificado previamente desde la interfaz web, la CLI fallará silenciosamente o restablecerá un usuario distinto al esperado.
2. **Entorno Aislado (Air-Gapped / Sin Salida a Internet):** Ante la ausencia del paquete binario `sqlite3` dentro del contenedor o en el Host, la imposibilidad de ejecutar `apt-get` o descargar imágenes de Docker auxiliares (ej. `keinos/sqlite3`) exige el uso de dependencias nativas del sistema operativo host.
3. **Persistencia por Variables de Entorno en Docker Compose:** Si las variables `GF_SECURITY_ADMIN_USER` o `GF_SECURITY_ADMIN_PASSWORD` están declaradas en el archivo `docker-compose.yml`, los reboots del contenedor sobreescribirán los cambios aplicados en la base de datos.

---

## 2. Requisitos Previos

- Acceso con privilegios de superusuario (`root` o vía `sudo`) a la máquina virtual / host Linux donde corre el _stack_ de monitoreo.
- La base de datos de Grafana debe utilizar la arquitectura por defecto basada en SQLite (`grafana.db` montado mediante _bind mount_ o _volume_).
- `python3` instalado en la máquina host (incluido por defecto en la biblioteca estándar de la mayoría de las distribuciones Linux).

---

## 3. Procedimiento Técnico Paso a Paso

### Paso 1: Detención Temporal del Servicio

Para evitar inconsistencias, contención de _locks_ o escrituras concurrentes en la base de datos SQLite mientras se modifica el archivo en disco, detenga el contenedor de Grafana:

```bash
docker compose stop grafana
```

_(Si no utiliza Docker Compose, puede ejecutar `docker stop <nombre_contenedor_grafana>`)_.

---

### Paso 2: Identificación de la Ruta de la Base de Datos

Ubique el archivo `grafana.db` en el sistema de archivos del Host. En estructuras típicas de Docker Compose con _bind mount_, la base de datos suele ubicarse en:

```text
./grafana/data/grafana.db
```

---

### Paso 3: Inspección del Usuario Principal con Python

Sin instalar binarios adicionales ni depender de red, utilice el módulo nativo `sqlite3` de Python 3 para consultar el estado del usuario con `id = 1`:

```bash
python3 -c "import sqlite3; conn = sqlite3.connect('./grafana/data/grafana.db'); cursor = conn.cursor(); print(cursor.execute('SELECT id, login, email FROM user WHERE id=1').fetchall())"
```

**Salida esperada:**

```python
[(1, 'usuario_actual', 'admin@localhost')]
```

_(Aquí observará cuál era el nombre de usuario al que la CLI no lograba aplicar el reseteo correctamente)._

---

### Paso 4: Actualización Directa del `login` a 'admin'

Ejecute el siguiente _one-liner_ de Python para forzar la actualización del campo `login` de la tabla `user` al valor predeterminado `admin`:

```bash
python3 -c "import sqlite3; conn = sqlite3.connect('./grafana/data/grafana.db'); cursor = conn.cursor(); cursor.execute(\"UPDATE user SET login='admin' WHERE id=1\"); conn.commit(); print('Usuario admin restaurado con éxito en la BD')"
```

---

### Paso 5: Reinicio del Servicio de Grafana

Inicie nuevamente el contenedor de Grafana para que el motor cargue el usuario actualizado:

```bash
docker compose start grafana
```

---

### Paso 6: Restablecimiento de Contraseña vía Grafana CLI

Una vez que el motor de Grafana está en ejecución y el usuario principal se llama nuevamente `admin`, ejecute la herramienta oficial CLI para blanquear y asignar una nueva contraseña (por ejemplo, `admin`):

```bash
docker compose exec grafana grafana cli reset-admin-password admin
```

**Salida de éxito:**

```text
INFO [...] Connecting to DB...
INFO [...] Starting DB migrations...
Admin password changed successfully ✔
```

---

### Paso 7: Verificación de Variables de Entorno en Docker Compose

Abra su archivo `docker-compose.yml` y asegúrese de eliminar o comentar las variables de entorno que pudieran sobreescribir las credenciales al reiniciar:

```yaml
services:
  grafana:
    image: grafana/grafana:latest
    environment:
      # Comentar o eliminar si existen:
      # - GF_SECURITY_ADMIN_USER=admin
      # - GF_SECURITY_ADMIN_PASSWORD=...
```

---

## 4. Validación del Acceso

1. Abra su navegador e ingrese a la URL/IP de su instancia de Grafana.
2. Inicie sesión con las credenciales:
    - **Usuario:** `admin`
    - **Contraseña:** `admin`
3. El sistema solicitará inmediatamente el cambio de contraseña por una definitiva.
4. **Verificación de Datos:** Confirme que todos los _Dashboards_, _Data Sources_ y alertas permanecen intactos.

---

## 5. Resumen del Flujo de Comandos

```bash
# 1. Detener el contenedor
docker compose stop grafana

# 2. Modificar el usuario ID 1 mediante Python nativo
python3 -c "import sqlite3; conn = sqlite3.connect('./grafana/data/grafana.db'); cursor = conn.cursor(); cursor.execute(\"UPDATE user SET login='admin' WHERE id=1\"); conn.commit()"

# 3. Iniciar el servicio
docker compose start grafana

# 4. Asignar la nueva contraseña mediante la CLI de Grafana
docker compose exec grafana grafana cli reset-admin-password admin
```