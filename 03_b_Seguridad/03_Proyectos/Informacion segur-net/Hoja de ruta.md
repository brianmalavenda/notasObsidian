tipo::
tags::
fecha:: 2026-08-25
links::
links_externos::

# Hoja de Ruta — Protocolo de Seguridad para Distribución de Materiales de Investigación vía Pendrive

## Fase 0 — Gobernanza y consenso institucional

**Qué hacer:** designar un responsable (o comité reducido) del protocolo, con autoridad para aprobar excepciones y coordinar entre docentes, investigadores y el área de sistemas. Comunicar el protocolo a todos los involucrados antes de aplicarlo.

**Por qué primero:** ningún control técnico funciona si no hay una autoridad clara que lo haga cumplir y una comunicación previa que evite que la gente busque atajos (ej. seguir usando pendrives personales por desconocimiento). Es la base de legitimidad para todo lo que sigue.

---

## Fase 1 — Contención inmediata de la topología de distribución

**Qué hacer:** pasar de "un pendrive circulando entre 30-50 alumnos" a un esquema en estrella: pendrive maestro del docente → pendrives individuales dedicados por alumno o por grupo de investigación, sin contacto entre pendrives de alumnos entre sí. Designar un único responsable por grupo de investigación como punto de contacto autorizado con los nodos.

**Por qué en esta fase:** es el cambio de menor costo y mayor impacto inmediato — no requiere hardware ni software nuevo, solo reorganizar el circuito físico. Reduce drásticamente la cadena de contagio y mejora la trazabilidad mientras se implementan los controles técnicos.

---

## Fase 2 — Control de dispositivos físicos

**Qué hacer:** adquirir pendrives dedicados provistos por la institución (no personales), preferentemente con protección física de solo-lectura. Establecer que todo dispositivo expuesto a una máquina no controlada se considera comprometido y se descarta, en vez de reformatear y reutilizar.

**Por qué en esta fase:** cierra la puerta a amenazas de firmware (tipo BadUSB) que ningún antivirus ni formateo puede detectar ni eliminar. Es una medida de bajo costo relativo que reduce un riesgo que las siguientes fases (basadas en software) no pueden cubrir por sí solas.

---

## Fase 3 — Estación de nodo segura (arranque en vivo)

**Qué hacer:** configurar los 5 nodos para operar con un sistema operativo "live" (de solo lectura, sin persistencia), con el disco de datos físicamente desconectado durante la etapa de recepción/escaneo de cada pendrive, y reconectado solo después de la verificación.

**Por qué en esta fase:** es el núcleo técnico del protocolo — convierte cada nodo en un punto de contacto que no puede quedar permanentemente comprometido, sin necesitar una estación de escaneo separada. Debe implementarse antes de habilitar el flujo normal de intercambio de archivos.

---

## Fase 4 — Control de ejecución y de dispositivos por software

**Qué hacer:** deshabilitar autorun/autoplay en todos los equipos, aplicar whitelisting de aplicaciones (solo ejecutables conocidos y firmados) y whitelisting de hardware por ID/número de serie para bloquear dispositivos no autorizados o reclasificación de clase de dispositivo (ej. un pendrive que intenta comportarse como teclado).

**Por qué en esta fase:** complementa la Fase 3 cerrando los vectores de ejecución automática y de dispositivos no autorizados, una vez que el nodo ya tiene una base de arranque segura sobre la cual aplicar estas políticas.

---

## Fase 5 — Verificación de integridad de archivos

**Qué hacer:** publicar el hash (SHA-256) de cada archivo original junto con su distribución, y exigir verificación de ese hash antes de aceptar cualquier archivo en el nodo.

**Por qué en esta fase:** una vez que el punto de contacto (nodo) está asegurado, este control garantiza que el contenido en sí no fue alterado ni reemplazado en el camino — cubre la amenaza de corrupción silenciosa de datos, que es más difícil de detectar que el borrado o el cifrado. Además, sienta las bases conceptuales para una eventual evolución hacia un esquema de verificación distribuida.

---

## Fase 6 — Monitoreo y trazabilidad

**Qué hacer:** habilitar logging local (inserciones de USB, ejecución de procesos, resultados de escaneo) en cada nodo, sin requerir conexión a internet.

**Por qué en esta fase:** con los controles preventivos ya en marcha (fases 1-5), el monitoreo pasa a cumplir su rol correcto: permitir reconstrucción forense si algo falla, no sustituir la prevención. Implementarlo antes sería prematuro sin los controles base funcionando.

---

## Fase 7 — Mantenimiento continuo

**Qué hacer:** definir una cadencia de reinstalación/reimageo periódico de los nodos a partir de una imagen base preparada (sistema Linux de código abierto, sin telemetría, con la configuración de seguridad de las fases anteriores ya incluida).

**Por qué en esta fase:** es la red de contención final — asume que, pese a todo lo anterior, algo puede eventualmente pasar desapercibido, y garantiza que ningún compromiso persista indefinidamente en el tiempo.

---

## Fase 8 — Evolución a mediano plazo

**Qué hacer:** evaluar una arquitectura de verificación distribuida (tipo blockchain/registro descentralizado) para la integridad de los materiales de investigación, construida sobre la base del hashing ya implementado en la Fase 5.

**Por qué al final:** requiere que las fases anteriores estén maduras y en funcionamiento estable; es una mejora estructural de largo plazo, no una respuesta a la contingencia actual.


---
#### Referencias

