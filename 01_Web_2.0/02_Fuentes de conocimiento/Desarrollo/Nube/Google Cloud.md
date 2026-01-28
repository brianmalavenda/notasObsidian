tipo:: #web/desarrollo
tags::
fecha:: 2025-09-09
links::
links_externos::

Para utilizar una API como text-to-speech, necesito crearme un usuario, proyecto, habilitar la API a la que quiero acceder a través del proyecto y generar una clave. Todos estos pasos son necesarios para que pueda generar un script de prueba desde mi PC.

## Instalar Google Cloud CLI

https://cloud.google.com/sdk/docs/install?hl=es-419#deb

Allí voy haciendo paso por paso la instalación para sistemas operativos Debian. 
Despues de hacer todos los pasos, cuando quiero iniciar el contexto de gcloud con el comando

```
	gcloud init
```

Me dio error y ejecute el comando para logearme 

```
	gcloud auth login
```

Pero continuaba el error. Finalmente, no había problema en la autenticación sino que desde el CLI no podía abrir ningún navegador para que pudiera logearme porque estoy ejecutando esto desde mi WSL en Windows. El comando que me ayudo a generar un link y abrir mi navegado fue 

```
	gcloud init --console-only
```

Ahora es necesario que genere un token Auth0 2.0 para acceder a un recurso privado como es **text-to-speech**. Para eso lo más sencillo fue obtenerlo directamente desde el script de pyhton con la biblioteca subprocess.check_output donde por línea de comando hubiera sido

```
	# bash
	gcloud auth print-access-token
	
	# python
	subprocess.check_output(['gcloud', 'auth', 'print-access-token'], text=True).strip()
```

### Formato de texto en SSML

https://cloud.google.com/text-to-speech/docs/ssml?hl=es-419

---
#### Referencias

