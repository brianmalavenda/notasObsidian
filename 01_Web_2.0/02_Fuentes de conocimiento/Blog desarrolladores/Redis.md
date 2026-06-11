tipo::
tags:: #rendimiento #cache #desarrollo/arquitectura 
fecha:: 2025-10-06
links::
links_externos::

## Definición
Redis es una **base de datos clave-valor en memoria**, extremadamente rápida. Redis es un servicio independiente. Tu aplicación se CONECTA a él como cliente.  Analogía: Como una base de datos PostgreSQL o MySQL pero optimizada para velocidad y cache
## Analogía práctica:
Imagina que Redis es como la **MEMORIA RAM de tu aplicación** pero persistente y compartida entre servicios.

## Características clave:
- **Datos en memoria**: Por eso es tan rápido (microsegundos)
- **Estructuras de datos ricas**: strings, lists, sets, hashes
- **Persistencia opcional**: Puede guardar en disco
- **Single-threaded**: Simplifica la concurrencia

```python
	# 1. Cache de estados de tareas
	redis.set("audio_task:12345:status", "processing")
	
	# 2. Resultados temporales
	redis.setex("audio_task:12345:result", 3600, audio_url)  # Expira en 1h
	
	# 3. Sesiones de usuario
	redis.set("user_session:abc123", user_data)

