tipo::
tags::
fecha:: 2025-10-06
links::
links_externos::

## Definición

^5256c4

RabbitMQ es un **"intermediario de mensajes"** que gestiona la comunicación entre servicios. ^definicion-rabbit
## Analogía práctica:
Imagina que RabbitMQ es el **SISTEMA POSTAL** de tu aplicación:
- Tú depositas una carta (mensaje) en la oficina postal (RabbitMQ)
- El cartero (worker) la recoge y la entrega
- Si no hay cartero disponible, la carta espera en la oficina
## Conceptos fundamentales:
- **Producer**: Quien envía mensajes
- **Consumer**: Quien recibe y procesa mensajes
- **Queue**: Cola donde se almacenan mensajes    
- **Exchange**: "Oficina postal" que enruta mensajes

---
#### Referencias

