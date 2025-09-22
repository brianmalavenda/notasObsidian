tipo:: #web/desarrollo 
tags::
fecha:: 2025-09-12
links::
links_externos::

---
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


# Daemon de docker esta inactivo

```
	systemctl start docker 
	systemctl status docker
```

si eso no funciona probar con esto que fue más efectivo
```
	sudo service docker restart
```
