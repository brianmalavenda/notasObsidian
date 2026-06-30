tipo:: Documentación técnica Proyecto
tags:: #n8n, #ollama, #raindrop, #automatización, #editorial
fecha:: 2026-06-25
links::
links_externos::
# Flujo de Trabajo Editorial Colaborativo (n8n + Ollama + Raindrop)

## Contexto y objetivo

Este documento adapta el flujo original de captura-resumen-archivo (pensado para un solo operador) a un esquema donde **varias personas seleccionan notas en paralelo**, agregando los tres controles que un flujo unipersonal no necesita pero uno de equipo sí: identificación de quién originó cada nota, deduplicación automática, y un punto de aprobación humana antes de que el contenido se considere "validado" en el vault.

El objetivo de fondo es transicionar de un portal de editoriales bimensuales a un diario web, repartiendo la carga de selección de noticias entre varias personas sin perder control de calidad editorial.

---

## Fase 1: Preparación del Entorno e Infraestructura

Mismo punto de partida que el flujo original, con dos agregados para soportar equipo.

1. **Levantar n8n (Self-Hosted)**: contenedor oficial, con volumen local montado para acceso al disco donde vivirá el Vault de Obsidian.    
2. **Levantar Ollama**: contenedor con un modelo intermedio-potente en español (`qwen2.5:7b` o `llama3:8b`).
3. **Crear cuenta en Raindrop.io**: instalar extensión de navegador (Brave/Firefox), crear colección `Inbox_Diario`.
4. **(Nuevo) Base de datos liviana para deduplicación**: levantar un contenedor de **SQLite** (o usar Postgres si ya existe en el homelab) con una tabla simple:
    
    ```sql
    CREATE TABLE notas_procesadas (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      url_normalizada TEXT UNIQUE,
      operador TEXT,
      fecha_procesado DATETIME DEFAULT CURRENT_TIMESTAMP,
      estado TEXT DEFAULT 'pendiente' -- pendiente | aprobado | descartado
    );
    ```
    
5. **(Nuevo) Canal de aprobación**: crear un bot de Telegram (vía [@BotFather](https://t.me/BotFather)) o un canal/webhook de Discord, dedicado exclusivamente a notificaciones de aprobación editorial. Es el punto donde el equipo va a aprobar/rechazar lo que la IA generó antes de que llegue al vault.
    

---

## Fase 2: Configuración del Flujo en n8n (Paso a Paso)

El flujo pasa de 5 nodos en línea recta a 7 nodos, con un punto de ramificación para deduplicación y otro para aprobación.

### Paso 1 — Disparador (Trigger de Raindrop, por operador)

- **Nodo**: `Raindrop Trigger` (o `Webhook` si se configura a mano).
- **Configuración**: vinculado a la cuenta de Raindrop compartida del equipo, escuchando el evento "New bookmark added" en la colección `Inbox_Diario`.
- **Cambio clave**: en lugar de una sola carpeta, cada persona guarda usando un **tag con su nombre** (`#brian`, `#colega1`, etc.) además de la colección. Raindrop entrega el tag dentro del JSON del bookmark, así que no hace falta pedirle a nadie que llene un formulario aparte — el "quién" sale gratis del mismo gesto de guardar el link.
- **Qué entrega**: JSON con URL, título original, fecha, y el tag del operador que lo guardó.

### Paso 2 — Normalización de URL y Deduplicación _(nodo nuevo)_

- **Nodo**: `Code` (JavaScript) → `Postgres`/`SQLite` (consulta).
- **Configuración**:
    1. Normalizar la URL: remover parámetros de tracking (`utm_*`, `?ref=`, `?fbclid=`, etc.) y forzar minúsculas en el dominio.
    2. Consultar la tabla `notas_procesadas` por esa URL normalizada en las últimas 48-72 horas.
    3. **Si ya existe** → cortar el flujo (rama `IF`) y opcionalmente notificar a ambos operadores que coincidieron en la selección (información útil para feedback editorial, no solo un descarte silencioso).
    4. **Si no existe** → insertar registro con estado `pendiente` y continuar el flujo.
- **Por qué importa**: sin esto, dos personas guardando la misma noticia viral duplican el gasto de cómputo de Ollama y generan dos archivos `.md` casi idénticos en el vault.

### Paso 3 — Extracción de Texto Limpio (Scraping)

- **Nodo**: `HTTP Request` (o Crawl4AI/Firecrawl si están containerizados).
- **Configuración**: método `GET`, URL dinámica desde el paso anterior.
- **Truco técnico**: pasar el HTML por un nodo `Code` con una librería tipo `Readability` para extraer solo el cuerpo del artículo, evitando que la IA lea menús, publicidad o navegación.

### Paso 4 — El Cerebro (Inferencia con Ollama)

- **Nodo**: `Ollama` (o `Advanced AI` de n8n apuntando al modelo local).
- **Configuración**: conexión a la IP local del servidor Ollama.
- **Prompt del sistema (actualizado con campos de confianza)**:
    
    > _"Actuás como un editor senior de un diario de informática. Vas a recibir el texto de una noticia. Tu tarea es devolver estrictamente un JSON con estos campos:_
    > 
    > - `resumen`: resumen ejecutivo en máximo 5 viñetas claras con datos duros.
    > - `categoria_madre`: exactamente una de estas 8 opciones: [Infraestructura, Internet, Seguridad, Monedas digitales, IA, Centros de datos, Energía, Desarrollo].
    > - `tags`: lista de palabras clave contextuales (empresas, protocolos, nombres propios).
    > - `confianza_categoria`: "alta", "media" o "baja", según qué tan seguro estás de la categoría asignada.
    > - `requiere_revision`: `true` si el texto scrapeado es corto, ambiguo, o la categoría es de confianza media/baja; `false` en caso contrario."
    
    **Por qué el agregado**: estos dos campos nuevos son los que permiten decidir automáticamente, en el paso siguiente, qué notas pueden pasar con revisión liviana y cuáles necesitan ojo humano antes de tocar el vault.
    

### Paso 5 — Aprobación Humana _(nodo nuevo, el cambio más importante)_

- **Nodo**: `Telegram` / `Discord` (envío de mensaje con botones) + `Webhook` (para recibir la respuesta).
- **Configuración**:
    1. n8n envía al canal de revisión: título original, resumen ejecutivo, categoría propuesta, y los campos `confianza_categoria` / `requiere_revision`.
    2. El mensaje incluye botones: **Aprobar** / **Editar categoría** / **Descartar**.
    3. La respuesta del equipo llega por webhook y actualiza el registro en la base de deduplicación (`estado` pasa de `pendiente` a `aprobado` o `descartado`).
    4. Si `requiere_revision = false` y `confianza_categoria = alta`, se puede configurar para que el flujo continúe automáticamente sin esperar aprobación manual (revisión liviana solo para los casos ambiguos) — esto evita que el equipo tenga que aprobar cada nota una por una si el volumen crece.
- **Por qué importa**: sin este paso, cualquier nota mal categorizada por el modelo se cuela directo al vault como si fuera contenido validado, y no hay forma de dar feedback editorial a quién seleccionó qué.

### Paso 6 — Formateo a Markdown

- **Nodo**: `Code` o `Set`.
    
- **Configuración**: armar el archivo `.md` con frontmatter ampliado para trazabilidad de equipo:
    
    ```markdown
    ---
    Fecha: {{ $json.fecha }}
    Origen: {{ $json.url_original }}
    Categoría: {{ $json.categoria_madre }}
    Tags: [{{ $json.tags }}]
    Seleccionado_por: {{ $json.operador }}
    Aprobado_por: {{ $json.revisor }}
    Confianza_categoria: {{ $json.confianza_categoria }}
    ---
    # {{ $json.titulo_original }}
    
    ## Resumen Ejecutivo
    {{ $json.resumen }}
    ```
    
    **Cambio clave**: los campos `Seleccionado_por` y `Aprobado_por` no estaban en el flujo original porque no hacían falta con un solo operador. Ahora dan, sin esfuerzo adicional, una bitácora editorial completa — útil para feedback de calidad y para auditar de dónde salió cada nota publicada.
    

### Paso 7 — Escritura en el Disco de Obsidian

- **Nodo**: `Write Binary File`.
    
- **Configuración**: montar como volumen la ruta del Vault de Obsidian.
    
- **Ruta dinámica de guardado** (sin cambios respecto al original):
    
    ```
    .../TuVaultObsidian/Fuentes/{{ $json.categoria_madre }}/{{ $json.titulo_limpio }}.md
    ```
    

---

## Fase 3: La Rutina Diaria del Equipo

Con el flujo adaptado, la rutina cambia ligeramente respecto al esquema unipersonal:

1. Cada persona del equipo lee sus fuentes (RSS en NewsBlur, redes, lo que sea) y guarda lo que le parece relevante en Raindrop, taggeado con su nombre.
2. n8n se activa, deduplica contra lo que ya guardaron otros, scrapea, y Ollama genera resumen + categoría + nivel de confianza.
3. Si la nota es de alta confianza y no requiere revisión → se archiva directo en el Vault.
4. Si la nota es ambigua o de baja confianza → llega al canal de Telegram/Discord para que alguien del equipo apruebe, corrija la categoría, o descarte.
5. A la tarde (o en el momento que corresponda según la cadencia editorial), quien redacta el portal abre Obsidian y encuentra las notas ya organizadas por categoría, con trazabilidad de quién la seleccionó y quién la aprobó.

---

## Próximos pasos sugeridos (no implementados aún en este flujo)

- **Calendario editorial**: decidir qué se publica qué día, para no acumular notas aprobadas sin publicar — relevante en cuanto la cadencia pase de bimensual a diaria.
- **Cuello de botella de redacción final**: aunque la selección esté distribuida entre varias personas, la escritura final del artículo para el portal sigue siendo manual y centralizada. Si el volumen crece, este es el siguiente punto a resolver.
- **Cola de revisión por lote**: si el equipo crece más allá de 2-3 personas, considerar reemplazar Telegram/Discord por una vista tipo tablero (Trello o similar) donde se aprueban varias notas pendientes de una sola vez, en lugar de una por una en tiempo real.