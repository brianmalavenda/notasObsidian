tipo::
tags::
fecha:: 2026-07-07
links::
links_externos::


### Opción 1: El enfoque estándar (Docker Hub)

Es justo lo que mencionas. Consiste en compilar tu imagen, subirla a un registro público o privado (como **Docker Hub** o **GitHub Packages**) y luego descargarla desde tu VM.

**Paso 1: Compilar y subir desde tu máquina local**

Bash

```
# 1. Iniciar sesión en Docker Hub
docker login

# 2. Compilar la imagen para producción asignándole tu usuario de Docker Hub
docker build -t tu_usuario_dockerhub/tts-sanmartin:latest -f backend/Dockerfile .

# 3. Subir la imagen a la nube
docker push tu_usuario_dockerhub/tts-sanmartin:latest
```

**Paso 2: Arrancar en Proxmox** Dentro de la máquina virtual (VM) que creaste en Proxmox (que debe tener Docker y Docker Compose instalados), modificas tu `docker-compose.yml` para que use esa imagen en lugar del `build`:

YAML

```
services:
  tts-sanmartin:
    image: tu_usuario_dockerhub/tts-sanmartin:latest # 👈 Ya no compila, la descarga directa
    container_name: tts-sm-container
    restart: unless-stopped
    ports:
      - "8000:8000"
    volumes:
      - ./audios:/tmp/doc2voz_output
```

Ejecutas `docker compose up -d` en Proxmox y listo.

### Opción 2: El enfoque Proxmox / Git (A veces más cómodo)

Si tu código ya está guardado en un repositorio privado (como GitHub o GitLab), muchas veces no hace falta pasar por Docker Hub.

1. Entras por SSH a tu VM en Proxmox.
    
2. Haces un `git clone` de tu repositorio allí mismo.
    
3. Corres tu `docker-compose.yml` original con la etiqueta `build:`. El propio Proxmox compilará la imagen internamente en un par de minutos.
    

Esto te ahorra tener que configurar cuentas en Docker Hub y hace que actualizar la app en producción sea tan fácil como hacer un `git pull` y `docker compose up -d --build` en Proxmox.

---
#### Referencias

