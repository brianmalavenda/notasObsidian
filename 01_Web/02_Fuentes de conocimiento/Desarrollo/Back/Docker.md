tipo:: #web/desarrollo 
tags::
fecha:: 2025-09-12
links::
links_externos::

---
### Instalar Docker

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

The Docker service starts automatically after installation. To verify that Docker is running, use:

```console
 sudo systemctl status docker
```

Some systems may have this behavior disabled and will require a manual start:

```console
 sudo systemctl start docker
```

Verify that the installation is successful by running the `hello-world` image:

```console
 sudo docker run hello-world
```

```
# Verificar que estás en el grupo docker
groups $USER
```

#### Evitar sudo
Una vez instalado vamos a crear un grupo y agregar nuestro usuario para evitar tener que poner el prefijo ```sudo``` antes de ejecutar cada comando

```console
# Agregar tu usuario al grupo docker
sudo usermod -aG docker $USER

# Reiniciar la sesión (cerrar y abrir terminal)
# O ejecutar este comando para aplicar los cambios sin reiniciar:
newgrp docker
```

### Reconstruir la imagen Docker

Ejecuta estos comandos:

```
docker-compose down -v
docker-compose build --no-cache
docker-compose up -d
```

Prueba la conexión manualmente desde el contenedor app:

``docker-compose exec app curl -X GET "http://es01:9200"

Ejecuta el indexador:

`docker-compose exec app python -m service.management.indexer -m index -n all -v

## Redes y volúmenes

https://www.youtube.com/watch?v=DIDel70dFlI

## Daemon de docker esta inactivo

```
	systemctl start docker 
	systemctl status docker
```

si eso no funciona probar con esto que fue más efectivo
```
	sudo service docker restart
```

## Debug

Ver uso de recursos
``` bash
	docker stats
```

Ver procesos dentro del contenedor
``` bash
	docker top backend-container
```

Inspeccionar contenedor
``` bash
	docker inspect backend-container
```

Ejecutar comando dentro del contenedor
``` bash
	docker exec -it backend-container bash
```

**En producción**

``` yml (docker-compose.yml)
	services:
	  backend:
	    image: backend
	    container_name: backend-container
	    logging:
	      driver: "json-file"
	      options:
	        max-size: "10m"
	        max-file: "3"
```
