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

Ver consumo de recursos por contenedor

``` bash
watch -n 2 "docker stats --no-stream"
```


# Seguridad

## Mejores Prácticas de Seguridad en la Red Virtual (Docker)

Tu principal preocupación es la seguridad de la red virtual. Aquí te muestro las prácticas clave para aislar tus aplicaciones:

### a) **Segmentación Estricta con Redes Personalizadas**

La buena práctica es crear una **red de Docker separada** para cada aplicación o grupo de servicios relacionados.

- **Evita usar la red `bridge` por defecto** para producción.
    
- Crea una red para tu _App A_ y otra para _App B_. Esto **aísla el tráfico**; si un contenedor en la red A es comprometido, no puede acceder a los contenedores en la red B por defecto.
    

> **Ejemplo:** En lugar de una sola red, usa:
> 
> - `docker network create app-a-net`
>     
> - `docker network create app-b-net`
>     
> - Conecta tus contenedores (front-end, back-end, DB) solo a su red de aplicación.
>     

### b) **Limitar Puertos Expuestos (Publicación)**

Solo debes publicar (`-p` o `ports:` en Docker Compose) los puertos que son **absolutamente necesarios** para el acceso externo.

- **Front-ends:** Solo expón el puerto HTTP/HTTPS (80/443 o similar).
    
- **Bases de Datos (DB) y Back-ends:** **Nunca** publiques los puertos de la base de datos (ej. 3306, 5432, 27017) o el back-end a tu red física si solo necesitan comunicarse con el front-end a través de la red virtual de Docker.
    

### c) **Firewall de Host (OpenMediaVault/OMV)**

Aunque OpenMediaVault usa un firewall, asegúrate de que **solo permita el tráfico** a los puertos que tus contenedores exponen al mundo exterior (ej. el 80 y 443 de tu proxy inverso).

- Bloquea cualquier otro puerto expuesto al exterior que no necesites.
    
- Esto actúa como una segunda capa de defensa.
    

### d) **Principio del Mínimo Privilegio (No Root)**

Asegúrate de que tus contenedores se ejecuten con un **usuario no root** siempre que sea posible.

- Añade la instrucción `USER` a tu Dockerfile (si construyes tus propias imágenes) o especifica la opción `user:` en tu archivo `docker-compose.yml`.
    
- Esto mitiga el riesgo de que un atacante, al comprometer un contenedor, obtenga privilegios de root en el sistema operativo de OMV.