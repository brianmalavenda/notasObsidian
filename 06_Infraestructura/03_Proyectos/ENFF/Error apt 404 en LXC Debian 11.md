---

title: 

- homelab
- proxmox
- lxc
- debian
- apt
- docker
- troubleshooting status: resuelto

---
## Resumen

Al ejecutar `apt update && apt upgrade -y` en un LXC con Debian 11 (bullseye) dentro de Proxmox VE 7.4, `apt` fallaba con decenas de errores `404 Not Found` contra `security.debian.org`. La causa era una línea mal escrita en `/etc/apt/sources.list`: faltaba `/debian-security` en la URL del repositorio de seguridad.

## Entorno

|Elemento|Valor|
|---|---|
|Host|Proxmox VE 7.4|
|Contenedor|LXC (hostname `nginx`)|
|SO del contenedor|Debian 11 (bullseye)|
|Objetivo|Actualizar el SO e instalar Docker CE + Compose plugin|

> [!note] Aclaración El contenedor se creyó Ubuntu, pero es **Debian 11**. Se nota en los sufijos de los paquetes (`+deb11u2`, etc.).

## Síntoma

```text
E: Failed to fetch http://security.debian.org/pool/updates/main/w/wget/wget_1.21-1%2bdeb11u2_amd64.deb  404  Not Found [IP: 151.101.130.132 80]
E: Failed to fetch http://security.debian.org/pool/updates/main/x/xz-utils/xz-utils_5.2.5-2.1%7edeb11u2_amd64.deb  404  Not Found
...
E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```

Todas las URLs fallidas tenían la forma `security.debian.org/pool/updates/...`.

## Diagnóstico

1. Los `404` aparecían solo en `security.debian.org`, no en `deb.debian.org`.
2. La ruta `/pool/updates/` es la estructura **antigua** del repo de seguridad.
3. Al revisar `/etc/apt/sources.list` se encontró esto:

```text
deb http://deb.debian.org/debian bullseye main
deb http://deb.debian.org/debian bullseye-updates main
deb http://security.debian.org bullseye-security main
```

La última línea no incluye `/debian-security`. Desde Debian 11 la URL correcta del repo de seguridad es `http://security.debian.org/debian-security`.

## Causa raíz

URL incorrecta del repositorio `bullseye-security` en `sources.list`. Además, los índices locales de `apt` podían estar desactualizados.

## Solución

### 1. Corregir `sources.list`

```bash
cat > /etc/apt/sources.list << 'EOF'
deb http://deb.debian.org/debian bullseye main
deb http://deb.debian.org/debian bullseye-updates main
deb http://security.debian.org/debian-security bullseye-security main
EOF

apt-get update
apt-get upgrade -y
```

### 2. Alternativa si siguen los errores (repos archivados)

Debian 11 ya terminó su soporte LTS, así que sus repositorios pueden moverse a `archive.debian.org`. En ese caso:

```bash
cat > /etc/apt/sources.list << 'EOF'
deb http://archive.debian.org/debian bullseye main
deb http://archive.debian.org/debian-security bullseye-security main
EOF

echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99archive

apt-get update
apt-get upgrade -y
```

> [!warning] Con `archive.debian.org` el contenedor funciona pero **ya no recibe parches de seguridad**.

### 3. Instalar Docker

Una vez que `apt-get update` termina sin errores:

```bash
apt install -y curl apt-transport-https ca-certificates gnupg lsb-release

mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list

apt update
apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

> [!bug] Error en el script original La línea del repo de Docker usaba `dpkg --architecture`, que no es un comando válido. Lo correcto es `dpkg --print-architecture`.

## Requisitos de Docker en un LXC

En Proxmox, en el contenedor: **Options → Features**, activar:

- `nesting=1`
- `keyctl=1`

Sin esto, Docker puede fallar al iniciar dentro del LXC.

## Verificación

```bash
apt-get update          # sin líneas Err: ni E:
apt-get upgrade -y      # sin 404
docker --version
docker compose version
systemctl status docker
```

## Lecciones aprendidas

- Un `404` masivo contra un solo host casi siempre apunta a una **URL mal escrita en `sources.list`**, no a un problema del paquete.
- Mirar siempre el principio de la salida de `apt update` (`Err:` / `E:`), no solo el final del `upgrade`.
- Para el repo de seguridad desde Debian 11 la forma correcta es `.../debian-security <release>-security`.
- Un `sources.list` mal escrito deja el sistema sin actualizaciones de seguridad sin avisar: conviene revisarlo al crear contenedores desde plantillas antiguas.

## Pendiente a mediano plazo

- [ ] Proxmox VE 7.4 y Debian 11 están fuera de soporte.
- [ ] Crear un LXC nuevo con **Debian 12** y migrar los servicios en vez de seguir actualizando este.

## Referencias

- Documentación de Debian sobre el repo de seguridad: `https://www.debian.org/security/`
- Instalación de Docker Engine en Debian: `https://docs.docker.com/engine/install/debian/`