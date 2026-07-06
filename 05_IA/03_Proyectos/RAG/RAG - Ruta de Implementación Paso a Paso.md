tipo::
tags::
fecha:: 2026-07-01
links::
links_externos::


### Resumen Tecnológico (Stack Open Source)

1. **Parseo:** `docling` (para transformar PDFs a Markdown estructurado).
2. **Base de Datos:** `Qdrant` (más simple de empezar que Milvus) corriendo en Docker.
3. **Embeddings:** `BAAI/bge-m3`.
4. **Framework:** `LangChain` o `LlamaIndex`.
5. **LLM:** Un modelo local con **`Ollama`** (`Llama 3` 70B o `Mistral`). Para empezar, `Llama 3 8B` es suficiente.
6. **Reranker (Opcional pero recomendado):** `BAAI/bge-reranker-v2-m3` corriendo en local.

No necesitas un superordenador. Con una RTX 3090/4090 (24GB VRAM) puedes correrlo cuantizado en 4-bit o 5-bit a buena velocidad. Para el procesamiento masivo de los 200 PDFs en la fase 2, puedes montar un script en `batch mode` y dejarlo corriendo una noche. La paciencia y la supervisión inteligente en el 10-15% inicial te ahorrarán un 90% de correcciones posteriores.
#### 1. Ingesta y Extracción del Texto (El Paso Más Crítico)

No puedes simplemente volcar PDFs. La historia tiene estructuras complejas (capítulos, pies de página, citas). Necesitas parsearlos de forma inteligente.
- **Herramienta:** No uses PyPDF2. Es obsoleta. La mejor librería hoy es **`docling`** (de IBM) o **`unstructured.io`**.
    - `docling`: Entiende la estructura compleja del documento (tablas, listas, diseño de dos columnas) y la convierte en Markdown, lo cual es oro puro para un LLM.
    - **Estrategia:** Procesa cada PDF a un archivo `.md` (Markdown). Esto preserva los encabezados (`# Título`, `## Capítulo`) que luego usarás para la ponderación temática.

#### 2. Estrategia de Fragmentación (Chunking) Inteligente para "Entrelazar"

Aquí es donde entra la "optimización de la relación entre ellos". Un chunking malo rompe el contexto a la mitad. El bueno lo preserva.
- **Fragmentación Semántica:** No cortes por tamaño fijo (ej. cada 500 tokens). Usa **`LangChain`** con `SemanticChunker` o `MarkdownHeaderTextSplitter`.
- **La Estrategia Maestra para tu caso:**
    1. Usa `MarkdownHeaderTextSplitter` para dividir por capítulos (`#`) y subcapítulos (`##`). Esto crea trozos temáticos naturales.
    2. Aplica un solapamiento generoso (20-25%). Si un chunk habla de "la Revolución Francesa", el siguiente también lo hará, pero desde otro ángulo. El solapamiento crea ese "entrelazado" que buscas.
    3. **Metadatos Enriquecidos (Aquí está la ponderación):** Cada fragmento debe guardar metadatos. Esto es clave para la ponderación.
        - `source`: Nombre del PDF.
        - `capítulo`: Título del capítulo.
        - `temas`: Usaremos un modelo simple (o LLM) para extraerlos: `["Revolución Industrial", "Marxismo", "Lucha de clases"]`.
        - `peso`: Un valor numérico que puedes calcular según la importancia del capítulo (introducción y conclusión tienen más peso) o la frecuencia de términos clave.

#### 3. Indexación en la Base de Datos Vectorial

- **Embeddings:** Usa **`BGE-M3`** (de BAAI). Es multilingüe y tiene un rendimiento excepcional en textos largos y densos como los históricos. Es de código abierto.
- **Base de Datos:** Olvídate de Pinecone (es de pago en la nube). Para +200 PDFs, necesitas una solución local robusta:
    - **`Milvus`** o **`Qdrant`** (ambas en Docker). Son las más rápidas y permiten filtrado por metadatos.
        

#### 4. Ponderación en la Búsqueda (Query-Time Weighting)

Aquí es donde implementas la "ponderación de palabras y temas". No solo buscas por similitud vectorial. Buscas con una **función híbrida**:
1. **Búsqueda Vectorial:** Encuentra fragmentos semánticamente similares a la pregunta del usuario.
2. **Filtro por Metadatos:** El usuario pregunta por "causas económicas de la Revolución Rusa".
    - Tu proceso debe: Identificar la entidad "Revolución Rusa" -> Filtrar metadatos `temas` que contengan "Revolución Rusa" Y "Economía" -> Aumentar la puntuación (`boost`) de esos fragmentos.
3. **Ponderación por `peso`:** Multiplica la puntuación de similitud por el campo `peso` del metadato. Así, un fragmento de la conclusión de un libro (peso alto) rankea antes que una nota al pie (peso bajo).
4. **Re-ranking:** Usa un modelo como **`Cohere Rerank`** o **`BGE-Reranker`** para reordenar los fragmentos ya recuperados y asegurarte de que los más relevantes estén al principio. Esto es magia para la calidad de la respuesta.
    

#### 5. El Agente de IA

Necesitas un framework que orqueste todo esto. **`LangChain`** o **`LlamaIndex`** son ideales. Personalmente, para tu caso, **LangChain (con LCEL - LangChain Expression Language)** te da un control más fino sobre el prompt y la cadena de recuperación.
- **Prompt del Sistema:** "Eres un historiador experto. Responde ÚNICAMENTE con la información proporcionada en el contexto. Si no encuentras la respuesta, indícalo claramente. Cita siempre la fuente (`source`) y el capítulo (`capítulo`) de donde extraes la información."
- **Memoria:** Tu agente debe recordar la conversación para poder "entrelazar" temas. "Háblame más de las consecuencias de lo que mencionaste antes".
    

---
