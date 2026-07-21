tipo:: 
tags:: #app #ia 
fecha:: 2026-07-10
links:: 
links_externos::

El procesamiento de cargas de trabajo basadas en Inteligencia Artificial —tales como modelos de generación de audio (_Text-to-Speech_) o transcripción (_Speech-to-Text_)— introduce un paradigma de alta latencia que rompe el ciclo tradicional de petición-respuesta HTTP de corto plazo. Cuando un backend ejecuta tareas intensivas utilizando únicamente la CPU del host, los tiempos de procesamiento escalan linealmente según la longitud del set de datos de entrada. 

Este informe analiza los cuellos de botella arquitectónicos y de infraestructura identificados al desplegar un servicio web de TTS utilizando un modelo llamado edge_tts, expuesto a producción bajo una arquitectura síncrona acoplada, detallando los fallos de red intermitentes, sus causas raíz y las soluciones aplicadas.

### Errores en Capas de Red

Al encontrarse el servicio detrás de múltiples capas de proxy inverso y redes de distribución de contenido (CDN), la latencia del backend gatilló fallas en cascada:

#### Bloqueo y Time-out en la Capa Edge (Cloudflare)

- **Síntoma:** Error `504 Gateway Time-out` (Cloudflare Ray ID / HTML corporativo).
    
- **Mecánica del fallo:** Cloudflare actúa como proxy inverso en la capa externa. Posee un límite estricto de tiempo de espera de **100 segundos** para la respuesta del servidor de origen (HTTP `Read Timeout`). Si la CPU del backend demora más de este umbral en generar el búfer del audio, Cloudflare asume que el origen está caído y corta la conexión unilateralmente.
    

#### Bloqueo en la Capa del Proxy Local (OpenResty / Nginx Proxy Manager)

- **Síntoma:** Error `504 Gateway Time-out` con firma de cabecera de `openresty`.
    
- **Mecánica del fallo:** Al omitir el proxy de Cloudflare (modo DNS Only), el tráfico impacta directamente en el proxy inverso local del ecosistema de contenedores. OpenResty viene preconfigurado con directivas de temporización por defecto (`proxy_read_timeout`) de **60 segundos**. Al superarse este minuto de procesamiento, el proxy local interrumpe el _stream_ hacia el cliente, devolviendo un documento HTML de error, a pesar de que el contenedor de Python sigue calculando el audio en segundo plano.
    
### Errores de datos

#### Excepción en el Parser del Frontend: Respuesta Inválida (Invalid JSON)

- **Síntoma:** `Unexpected token '<', "<html> <h"... is not valid JSON`.
    
- **Mecánica del fallo:** El cliente JavaScript (Frontend) realiza una petición `fetch()` esperando un objeto estructurado (`application/json`). Al producirse los _time-outs_ descritos arriba, los proxies interceptan la comunicación y devuelven una página de error estructurada en formato HTML. El método ejecutor `.json()` de la API Fetch falla catastróficamente al intentar parsear el carácter inicial `<` propio de las etiquetas HTML.

#### Rechazo de Carga Útil en Capas de Terceros (Telegram API: Error 400 Bad Request)

- **Síntoma:** `{detail: "Telegram rechazó el envío. Revisá el token y el chat_id"}`.
    
- **Mecánica del fallo:** Además de la latencia, la inyección de datos de configuración incorrectos en las variables de entorno (`.env`) en caliente provocó fallas de validación. En grupos de Telegram, los identificadores numéricos (_Chat IDs_) son estructuralmente **negativos** (anteponiendo el carácter `-`). Al enviarse como un entero positivo, la API de Telegram mapeaba la petición hacia un endpoint de chat privado inexistente, denegando el envío del log/notificación multimedia.

## Soluciones Implementadas (Mitigación en Capa de Infraestructura)

Para estabilizar el entorno de producción y permitir la validación del backend sin interrupciones de red, se aplicaron modificaciones de infraestructura desacopladas del código de la app:

- **Paso de CDN a DNS Puro (Nube Gris en Cloudflare):** Se desactivó el proxy de Cloudflare para eliminar el límite infranqueable de los 100 segundos, permitiendo que el tráfico llegue directo al balanceador local.
    
- **Tuning de Directivas de Temporización en OpenResty:** Se incrementaron los umbrales de tolerancia del proxy inverso local en Nginx Proxy Manager a un máximo de 5 minutos (300 segundos) mediante inyección de bloques de configuración avanzada:
    
    Nginx
    
    ```
    proxy_connect_timeout       300;
    proxy_send_timeout          300;
    proxy_read_timeout          300;
    send_timeout                300;
    ```
    
- **Ciclo de Vida Correcto en Docker Compose:** Se identificó que Docker Compose no recrea los contextos de entorno con un simple comando `restart`. Se procedió a estructurar el despliegue con `docker compose down && docker compose up -d` para forzar la lectura del archivo `.env` en disco e inyectar el identificador de chat corregido (`TELEGRAM_CHAT_ID=-5369331289`) y las cuotas de caracteres actualizadas.
    

>[!IMPORTANT]
Mantener una conexión HTTP síncrona bloqueada por minutos degrada severamente los recursos del servidor y limita la concurrencia a un puñado de usuarios simultáneos. Para transformar esta herramienta en una aplicación altamente escalable, se deben adoptar los siguientes patrones de diseño de software:

### Desacoplamiento mediante Arquitectura Orientada a Eventos (Asincronismo)

Se debe reemplazar el flujo síncrono actual por un procesamiento asíncrono basado en colas de tareas utilizando la siguiente arquitectura:

1. **Ingesta Inmediata (HTTP 202 Accepted):** El Frontend envía el texto a procesar. El Backend valida la estructura del payload, persiste una entrada en base de datos con un estado `en_progreso`, encola la tarea y retorna de inmediato un JSON con un identificador único de tarea (`task_id`) en milisegundos.
    
2. **Procesamiento en Segundo Plano (Workers):** Una herramienta de manejo de colas como ==**Celery** o **Redis Queue (RQ)**== toma la tarea de la cola. Un proceso independiente (_Worker_) se encarga de interactuar con el modelo de IA. Con este enfoque, el servidor web (FastAPI/Uvicorn) queda libre para seguir recibiendo peticiones.
    
3. **Mecanismo de Consumo del Frontend:**
    - **Opción A (Polling):** El cliente realiza peticiones periódicas (ej. cada 5 segundos) a un endpoint del tipo `/api/status/{task_id}` hasta que el estado cambie a `completado` y devuelva la URL final de descarga del archivo de audio persistido.
    - **Opción B (WebSockets / Server-Sent Events):** Se abre un canal de comunicación bidireccional permanente entre el navegador y el servidor. El backend notifica de manera reactiva al cliente el progreso exacto y la finalización de la síntesis de voz.

### 4.2. Implementación de una Capa de Sanitización y Seguridad Avanzada

Para mitigar denegaciones de servicio (DoS) por sobrecarga de memoria en el modelo de IA ante strings maliciosos o archivos corruptos extraídos de PDFs/Docx, se propone estructurar una función de limpieza estricta por lista blanca en la API:

```Python
import re
import unicodedata
import logging

logger = logging.getLogger("security")

def sanitizar_entrada_texto(raw_text: str) -> str:
    if not raw_text:
        return ""
    
    # 1. Normalización Unicode (Evita descalces de codificación)
    text = unicodedata.normalize('NFC', raw_text)
    
    # 2. Stripping de tags HTML/XML (Previene inyecciones básicas de marcado)
    text = re.sub(r'<[^>]*>', '', text)
    
    # 3. Lista blanca de caracteres admisibles (Alfanuméricos, acentos, eñes y puntuación humana)
    clean_pattern = re.compile(r'[^a-zA-Z0-9áéíóúÁÉÍÓÚñÑüÜ\s\.,!\?:\;\-_"\'\(\)]')
    sanitized_text = clean_pattern.sub('', text)
    
    # 4. Unificación de espacios, tabulaciones y saltos de línea destructivos
    sanitized_text = re.sub(r'\s+', ' ', sanitized_text).strip()
    
    return sanitized_text
```


---
#### Referencias

