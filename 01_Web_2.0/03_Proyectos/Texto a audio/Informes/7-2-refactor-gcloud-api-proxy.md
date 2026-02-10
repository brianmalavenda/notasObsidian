tipo::
tags::
fecha:: 2026-02-07
links::
links_externos::

## 01 - Validación de Credenciales vs. Existencia de Archivo

### descripcion

El código original solo verificaba si el archivo `google-credentials.json` existía físicamente en el volumen de Docker. Sin embargo, esto no garantizaba que las credenciales fueran válidas, que el JSON tuviera el formato correcto o que la cuenta de servicio estuviera activa en Google Cloud. Esto causaba errores de autenticación que no eran capturados hasta que se intentaba usar la API de Text-to-Speech.

### solucion

Se implementó un "Health Check" real utilizando el método `credentials.refresh(Request())`. Esto fuerza una llamada de red a los servidores de Google. Si el token no se genera correctamente, el sistema captura el error de autenticación antes de procesar cualquier audio, permitiendo distinguir entre un fallo de infraestructura (archivo no encontrado) y uno de credenciales (llave privada inválida).

## 03 - Retorno de Audio en Sintetización Larga

### descripcion

La API `synthesize_long_audio` es asíncrona y, por diseño de Google, no devuelve los bytes del audio en la respuesta inmediata, sino que deposita el resultado en un bucket de Google Cloud Storage. Esto impedía que el cliente recibiera el archivo de audio directamente tras la solicitud.

### solucion

Se integró la librería `google-cloud-storage`. Tras completar la operación de síntesis con `operation.result()`, el código ahora accede al bucket de GCS, localiza el archivo generado por Google y descarga sus bytes mediante `blob.download_as_bytes()`. Estos bytes son los que finalmente se retornan al cliente a través del endpoint.

## 04 - Fragmentación de Texto en SSML

### descripcion

El archivo SSML original enviaba cada frase o fragmento dentro de etiquetas `<prosody>` individuales. En la síntesis de voz, esto provoca que la IA realice micro-pausas artificiales entre etiquetas, resultando en una narración entrecortada y poco natural (efecto robótico).

### solucion

Se optimizó el SSML agrupando el texto en párrafos lógicos y eliminando etiquetas redundantes. Se introdujo el uso de `<say-as interpret-as="characters">` para códigos alfanuméricos y etiquetas `<break time="1s"/>` para separar noticias. Esto permite que el motor de TTS mantenga la entonación y fluidez correctas durante toda la lectura del bloque de texto.


## 05 - Error de Importación de Storage

### descripcion

Al intentar usar `from google.cloud import storage`, el sistema arrojaba un `ImportError`. Esto ocurría porque las librerías de Google Cloud son modulares; tener instalado el paquete de Text-to-Speech no incluye automáticamente el paquete de Cloud Storage.

### solucion

Se identificó la necesidad de instalar el paquete específico `google-cloud-storage`. La solución consiste en añadir esta dependencia al archivo `requirements.txt` del proyecto y reconstruir el contenedor de Docker para que el espacio de nombres `google.cloud` reconozca el módulo de almacenamiento.

## 06 - Error de Git (Socket y Autenticación)

### descripcion

Al intentar hacer `push`, surgió un error de conexión con el socket de VS Code (`ECONNREFUSED`) y un fallo de autenticación remota. Esto suele ocurrir cuando el puente de credenciales de VS Code falla o cuando se intenta acceder de forma anónima a un repositorio privado desde dentro de un contenedor.

### solucion

Se recomendó limpiar las variables de entorno de Git Askpass o, de forma más efectiva, actualizar la URL del remoto para incluir un Personal Access Token (PAT). Esto permite que Git se autentique directamente sin depender del socket de VS Code, asegurando el acceso de escritura al repositorio en GitHub.

## 1 - Conectividad entre Contenedores Docker

### Descripción

El contenedor backend no podía conectar a api-proxy a pesar de estar en la misma red Docker. El DNS resolvía la IP pero los paquetes se perdían. Postman funcionaba pero entre contenedores había timeout.

### Solución

La política iptables FORWARD estaba en DROP (causado por VPN). Se restauró con `sudo iptables -P FORWARD ACCEPT` y se añadieron reglas persistentes en DOCKER-USER para permitir tráfico entre bridges Docker.

---

## 2 - Paquete Python No Actualizado en Contenedor

### Descripción

Cambios en el código de gcloud_tts no se reflejaban en el contenedor. El build usaba cache de Docker y el paquete instalado en site-packages era versión vieja con validación de project_id obligatoria.

### Solución

Se eliminó el modo editable problemático, se unificó la estructura de paquetes y se reconstruyó con `--no-cache`. Se consolidó todo en un solo paquete `api_proxy` con módulo interno `gcloud_tts`.

---

## 3 - Entrypoint No Encontrado en Imagen Docker

### Descripción

El contenedor fallaba al iniciar con error "docker-entrypoint.sh: executable file not found in $PATH". El Dockerfile referenciaba un script que no existía o no era ejecutable.

### Solución

Se sobrescribió el entrypoint en los comandos docker run usando `--entrypoint python` o se modificó el Dockerfile para usar `ENTRYPOINT ["python", "-m", "api_proxy"]` directamente sin script externo.

---

## 4 - Envío de Archivos en Postman

### Descripción

La API rechazaba peticiones con error "No file part". Postman mostraba el archivo seleccionado pero el backend no lo recibía en `request.files`.

### Solución

El campo `file` en Postman estaba configurado como "Text" en lugar de "File". Se cambió el tipo a "File" y se seleccionó el archivo físico desde disco, permitiendo que multipart/form-data funcionara correctamente.

## 7 - Copia de Secrets en Imagen Docker

### Descripción

El build fallaba con "secrets/: not found" al intentar copiar credenciales. La carpeta secrets estaba fuera del contexto de build o excluida por .dockerignore.

### Solución

Se movió la carpeta secrets dentro de api_proxy/ y se corrigió el COPY en Dockerfile. Se eliminó `secrets/` de .dockerignore para permitir la copia durante el build.

---

## 8 - Contenedor Exite Inmediatamente

### Descripción

El contenedor se creaba pero estado mostraba "Exited (0)" sin errores. No quedaba corriendo el servicio.

### Solución

El comando docker run ejecutaba `python` sin argumentos. Se añadió `-m api_proxy` para especificar el módulo a ejecutar, o se corrigió el ENTRYPOINT/CMD en Dockerfile para incluir el módulo.


---
#### Referencias

