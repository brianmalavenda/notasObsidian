tipo::
tags::
fecha:: 2025-10-06
links:: [[Redis]] [[RabbitMQ]]
links_externos::

FRONTEND → BACKEND (Producer) → RABBITMQ (Queue) → API-PROXY (Consumer) → GOOGLE TTS
                    ↓                                       ↓
                 REDIS (Estado)                          REDIS (Resultado)
                    ↓
              FRONTEND (Consulta estado)
          

1. **Frontend** → Backend: "Quiero sintetizar este texto"
2. **Backend** → RabbitMQ: "Mensaje: sintetizar texto X" (→ Respuesta inmediata al frontend)
3. **RabbitMQ** guarda el mensaje en cola
4. **API-Proxy** (worker) toma el mensaje cuando está libre
5. **API-Proxy** → Google TTS: Sintetiza audio
6. **API-Proxy** → Redis: Guarda URL del audio resultante - esto supone el bucket o el blob público
7. **Frontend** (polling) → Backend → Redis: "¿Está listo el audio?"

- ✅ **Desacoplamiento total** entre backend y API-Proxy
- ✅ **Fácil cambiar proveedores** de TTS
- ✅ **Escalabilidad horizontal** de workers
- ✅ **Resiliencia** - mensajes persisten si un worker falla
- ✅ **Tracking en tiempo real** del estado

## 🎯 **Redis - Base de Datos en Memoria**

### ¿Qué es?

![[Redis#Definición]]
## 🐇 **RabbitMQ - Broker de Mensajería**

### ¿Qué es?

![[RabbitMQ#^definicion-rabbit]]

### 🏗️ **Arquitectura de Producer-Consumer**

### Producer (Productor)
**TU BACKEND actúa como productor**, genera la tarea y la deja encolada para que se procese

```python
	class AudioTaskProducer:
	    def publish_synthesis_task(self, text):
	        # Crea el "sobre" con la tarea
	        task = {'task_id': '123', 'text': text}
	        
	        # Lo envía al "buzón" (RabbitMQ)
	        rabbitmq.send(task)
	        
	        # Respuesta INMEDIATA al frontend
	        return {'task_id': '123', 'status': 'queued'}

```
### Consumer (Consumidor)
**TU API-PROXY actúa como consumidor**, ya que "toma" la tarea encolada en RabbitMQ y la realiza. Una vez que termino la acción pedida en la tarea, actualiza el "estado" en un listado de tareas pendientes en **Redis**

```python
	class AudioSynthesisWorker:
    def process_task(self, task):
        # Escucha constantemente el "buzón"
        # Cuando llega un mensaje, lo procesa
        
        audio_url = google_tts.synthesize(task['text'])
        
        # Guarda el resultado en Redis (cache rápido)
        redis.set(f"task:{task['task_id']}:result", audio_url)
        
```
        
###  **Patrón Strategy - Para múltiples proveedores TTS**
**¿Qué problema resuelve?**
Quieres poder cambiar fácilmente entre Google TTS, Azure TTS, Amazon Polly, etc.

```python
	# ARCHITECTURA LIMPIA - fácil de extender
	class TTSProvider(ABC):
	    @abstractmethod
	    def synthesize(self, text): pass
	
	class GoogleTTSProvider(TTSProvider):
	    def synthesize(self, text): 
	        # implementación Google
	
	class AzureTTSProvider(TTSProvider):  
	    def synthesize(self, text):
	        # implementación Azure
	
	# Uso uniforme
	provider = providers[provider_name]  # 'google' o 'azure'
	audio_url = provider.synthesize(text)  # Misma interfaz para todos
```

docker-compose.yml

```yml 
	version: '3.8'
	services:
	  backend:
	    build: ./backend
	    depends_on:
	      - redis
	      - rabbitmq
	  
	  api-proxy:
	    build: ./api-proxy
	    depends_on:
	      - redis
	      - rabbitmq
	  
	  redis:
	    image: redis:alpine
	    ports:
	      - "6379:6379"
	  
	  rabbitmq:
	    image: rabbitmq:3-management
	    ports:
	      - "5672:5672"
	      - "15672:15672"
```

---
#### Referencias

