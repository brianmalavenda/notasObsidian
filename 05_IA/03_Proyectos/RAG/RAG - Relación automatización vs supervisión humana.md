tipo::
tags::
fecha:: 2026-07-01
links::
links_externos::

| Fase                          | % IA | % Humano                                           | Rol de GLM-4 / Qwen 2.5                                     |
| ----------------------------- | ---- | -------------------------------------------------- | ----------------------------------------------------------- |
| **1. Parseo PDFs**            | 95%  | 5% (QA y decisión OCR)                             | Validador de calidad del Markdown generado.                 |
| **2. Chunking y Ponderación** | 80%  | 20% (Definición de criterios y validación inicial) | Divisor semántico, extractor de temas y asignador de pesos. |
| **3. Indexación y Búsqueda**  | 99%  | 1% (Ajuste de fórmula de búsqueda)                 | Poco (es más matemático).                                   |
| **4. Agente RAG**             | 85%  | 15% (Prompt design y pruebas de estrés)            | Asistente de código y generador de prompts.                 |
| **5. Fine-Tuning**            | 30%  | 70% (Curación de datos, evaluación)                | Generador de datasets sintéticos.                           |

# Detalle
### Fase 1: Parseo de PDF a Markdown (5% Humano - 95% IA)

Este es el paso más susceptible de automatizar, pero con un matiz importante.
**Automatización con IA (95%):**  
Usar `docling` o `marker` (una librería open-source que convierte PDF a Markdown usando modelos de deep learning) es un proceso por lotes. Apuntas a tu carpeta de 200 PDFs y lo procesa todo.

- **Dónde usar GLM/Qwen:** No directamente en el parseo, sino como **validador**. Puedes crear un script que, para cada PDF, tome páginas aleatorias y se las pase a GLM-4 9B con un prompt:
    > "Compara el texto original de esta página con el markdown generado. ¿Hay errores de OCR? ¿Se ha respetado la jerarquía de títulos? ¿Las tablas se han formateado correctamente? Responde con una valoración del 1 al 10 y señala los problemas."
- **Trabajo Humano (5%):**
    - Revisar la muestra que el validador de IA marcó con baja puntuación (<7/10).
    - Decidir qué hacer con PDFs escaneados (imágenes en lugar de texto). Si tienes muchos, necesitarás un módulo de OCR. `docling` ya lo integra, pero para textos históricos complejos, puede que necesites supervisar la calidad. La decisión de "este PDF es inusable sin un OCR más potente" es humana.

**Porcentaje final:** 5% supervisión humana para aseguramiento de calidad, 95% automatizado.

---
### Fase 2: Fragmentación (Chunking) y Ponderación de Temas (20% Humano - 80% IA)

Aquí la cosa cambia. La estrategia de fragmentación es un arte, no una ciencia exacta.

**Automatización con IA (80%):**
- **Fragmentación semántica guiada:** Le puedes pasar un capítulo entero a GLM-4 9B (con su contexto de 128K tokens) y decirle:
    > "Divide este texto en fragmentos autónomos que traten un único tema o evento. El primer fragmento debe ser una introducción, el último una conclusión. Para cada fragmento, asígnale un peso del 1 al 5 según su importancia estructural (introducción=5, conclusión=5, desarrollo=3, ejemplo=1)."
- **Extracción de metadatos ponderados:** Tras la división, otro prompt para cada fragmento:
    > "Extrae los 3 temas principales y 5 palabras clave más relevantes, con un peso del 1 al 10 para cada una en función de su centralidad en el párrafo. Los personajes históricos mencionados deben tener peso alto."

**Trabajo Humano (20%):**
- **Definir la taxonomía de pesos y temas:** Antes de automatizar, debes decidir qué es "importante" para tu dominio. ¿Un personaje vale más que una fecha? ¿Una causa económica pesa más que una anécdota? Esta definición del criterio es 100% humana.
- **Validar la fragmentación de los primeros 5-10 PDFs:** Revisarás que GLM no esté rompiendo argumentos complejos por la mitad o asignando pesos incorrectos. Una vez validado, el script se aplica al resto.
- **Crear una lista de "temas pivotales":** Para unificar criterios, puedes darle a la IA un tesauro con los términos que consideras centrales (ej: "Lucha de clases", "Materialismo histórico") para que los pondere más alto.

**Porcentaje final:** 20% inicial de supervisión y definición de criterios, 80% automatizado una vez calibrado.

---

### Fase 3: Indexación y Búsqueda Híbrida (1% Humano - 99% IA)

Esta fase es puramente técnica.

**Automatización (99%):**  
Una vez que tienes los fragmentos con sus metadatos (temas, pesos), el proceso de generar embeddings (con `BGE-M3`) y subirlos a Qdrant es un script completamente automático.  
La búsqueda híbrida (vectorial + filtro por peso) es una función matemática que no requiere intervención. Tú configuras la fórmula:  
`score_final = (score_vectorial * 0.7) + (peso_del_documento * 0.3)`  
y el sistema la aplica solo.

**Trabajo Humano (1%):**
- Ajustar los coeficientes de la fórmula anterior (0.7 y 0.3) tras las primeras pruebas. "¿Está dando demasiado peso al vector y poco a mi ponderación humana?". Ese tweaking es manual.

---

### Fase 4: Creación del Agente RAG y Refinamiento del Prompt (15% Humano - 85% IA)

Aquí la IA te ayuda a construir el agente, pero la visión estratégica es humana.

**Automatización con IA (85%):**  
Puedes usar GLM-4 en modo "asistente de código" para que te genere el script de LangChain con:
- El prompt del sistema.
- La función de recuperación híbrida de Qdrant.
- La memoria de la conversación.
- La orden de citar fuentes.

**Trabajo Humano (15%):**

- **Diseñar la personalidad y los límites del agente:** El tono, el rigor, las instrucciones de "no alucinar", el formato de cita. Esto es artesanía de prompt engineering.
- **Pruebas de estrés:** Hacerle preguntas con trampa ("¿Qué dijo Napoleón sobre internet?"), preguntas con sesgo ("¿Por qué el capitalismo es malo?"), y preguntas muy amplias. Analizar las respuestas y ajustar el prompt o los parámetros de búsqueda. La IA no sabe hacer esto; eres tú viendo si el agente es el historiador que quieres crear.
    

---

### Fase 5 (Opcional): Fine-Tuning del Modelo (70% Humano - 30% IA)

Esto es si decides ir al paso de "crear tu propio LLM".

**Automatización (30%):**  
GLM te puede ayudar a generar datasets sintéticos de preguntas y respuestas a partir de tus fragmentos para el entrenamiento.

**Trabajo Humano (70%):**

- Curar ese dataset. La calidad de los datos de entrenamiento lo es todo.
- Evaluar los checkpoints del modelo. "¿Está mejorando o empeorando con el fine-tuning?".
- Preparar la infraestructura de entrenamiento (GPU, scripts con Unsloth/LLaMA-Factory). Aunque el código te lo de la IA, entender los hiperparámetros y tomar las decisiones es muy manual.

---
#### Referencias

