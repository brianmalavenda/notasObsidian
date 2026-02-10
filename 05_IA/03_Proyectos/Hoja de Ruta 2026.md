tipo::
tags::
fecha:: 2026-02-02
links::
links_externos::


| Fase  | Objetivo                         | Duración     | Tu Primer Output Vendible     |
| ----- | -------------------------------- | ------------ | ----------------------------- |
| **0** | Python + Fundamentos Matemáticos | 4-6 semanas  | —                             |
| **1** | Machine Learning Clásico         | 6-8 semanas  | —                             |
| **2** | Deep Learning (desde cero)       | 8-10 semanas | Prototipo técnico             |
| **3** | LLMs, RAG y Embeddings           | 6-8 semanas  | Agente interno funcional      |
| **4** | Agentes de IA y Orquestación     | 8-10 semanas | **Agente MVP** (beta testers) |
| **5** | Producción + Go-to-Market        | 8-12 semanas | **Primer cliente pagando**    |


## 🧱 FASE 0: Fundamentos (Semanas 1-6)

**Objetivo:** Dominar Python científico y álgebra lineal suficiente para no ser un "copia-pega".

### Recursos Open Source (100% gratis)

1. **[ML for Beginners](https://github.com/microsoft/ML-For-Beginners)** - Microsoft (12 semanas, pero haz las primeras 4)
    
2. **[Python for Data Science](https://github.com/jakevdp/PythonDataScienceHandbook)** - Jake VanderPlas
    

### Proyecto Clave

- **"Predicción de precios inmobiliarios"** desde cero con NumPy (sin scikit-learn todavía). Debes entender qué hace `.fit()` por dentro.
    

### Checkpoint de Salida

- Resuelves ecuaciones lineales con matrices
    
- Entiendes qué es el gradiente descendiente sin ver un video de YouTube
    

---

## 🌱 FASE 1: Machine Learning Tradicional (Semanas 7-14)

**Objetivo:** Entender el flujo completo de datos antes de meter redes neuronales.

### Recursos Clave

1. **[AI for Beginners](https://github.com/microsoft/AI-For-Beginners)** - Microsoft (curso completo)
    
2. **[Made With ML](https://github.com/GokuMohandas/Made-With-ML)** - MLOps desde el día 1 (crítico para comercializar después)
    

### Proyecto Clave

- **Clasificador de emails** con Random Forest + pipeline completo de deployment con Docker (aprende Docker aquí, no después).
    

### Checkpoint

- Tienes un modelo en producción local (FastAPI + Docker) que responde a POST requests.
    

---

## 🧠 FASE 2: Deep Learning Desde Cero (Semanas 15-24)

**Esta es la fase más importante.** Si saltas directo a LangChain sin esto, serás un "configurador de prompts", no un ingeniero de agentes.

### Recursos Clave (Obligatorios)

1. **[Neural Networks: Zero to Hero](https://github.com/karpathy/nn-zero-to-hero)** - Andrej Karpathy
    
    - No sigas hasta que implementes backpropagation manualmente en NumPy
        
    - Construye tu propio GPT-2 pequeño (nanoGPT)
        
2. **[Deep Learning Paper Implementations](https://github.com/lucidrains/deep-learning-paper-implementations)** - Implementaciones de Transformers en PyTorch puro
    

### Proyecto Clave

- **Tu propia red neuronal de clasificación** escrita en NumPy desde cero (sin PyTorch/TensorFlow)
    
- Luego, fine-tuning de un LLM pequeño (Qwen 2.5 de 1.8B parámetros) para una tarea específica
    

### Checkpoint

- Puedes explicar qué hace el mecanismo de atención sin usar la palabra "magia"
    
- Entiendes el problema del "vanishing gradient"
    

---

## 🗣️ FASE 3: LLMs, RAG y Embeddings (Semanas 25-32)

**Objetivo:** Hacer que tu modelo "conozca" datos privados y responda conversacionalmente.

### Recursos Clave

1. **[Hands-On LLMs](https://github.com/HandsOnLLM/Hands-On-LLMs)** - Libro open source con código
    
2. **[Advanced RAG Techniques](https://github.com/NirDiamant/RAG_Techniques)** - 30+ técnicas de RAG (Hybrid Search, GraphRAG, etc.)
    

### Stack Técnico a Dominar

- **Embeddings:** Sentence Transformers, OpenAI Ada-003, o modelos locales (BGE-M3)
    
- **Vector DBs:** Chroma (local), Qdrant o Weaviate (producción)
    
- **Frameworks:** LangChain (básico) o LlamaIndex (para RAG complejos)
    

### Proyecto Clave

- **"Chat con tus PDFs"** pero empresarial: procesa 1000 PDFs, chunking inteligente, recuperación híbrida (semántica + lexical), evaluación con métricas (RAGAS).
    

### Checkpoint

- Tu sistema responde preguntas complejas tipo: "¿Cuál fue el margen de beneficio en Q3 mencionado en los informes del 2023?" con citas exactas.
    

---

## 🤖 FASE 4: Agentes de IA (Semanas 33-42)

**Aquí construyes el cerebro que toma decisiones.**

### Frameworks Open Source 2025 (elige 2)

| Framework                                                               | Caso de Uso                                             | Aprendizaje                     |
| ----------------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------- |
| **[LangGraph](https://github.com/langchain-ai/langgraph)**              | Agentes stateful, multi-pasos, human-in-the-loop        | Enterprise, control total       |
| **[CrewAI](https://github.com/joaomdmoura/crewAI)**                     | Equipos de agentes con roles (vendedor, analista, etc.) | Rápido, menos código            |
| **[AutoGen](https://github.com/microsoft/autogen)**                     | Colaboración multi-agente compleja                      | Research, workflows científicos |
| **[OpenAI Agents SDK](https://github.com/openai/openai-agents-python)** | Prototipado rápido, tracing integrado                   | MVPs ágiles                     |
| **[Dify](https://github.com/langgenius/dify)**                          | Low-code + código, autohosteo completo                  | No-code a código real           |
### Arquitectura que Debes Dominar

Usuario → Router Agent → Tool Agent (APIs) / RAG Agent / Coding Agent
                ↓
         Memory (Redis/Postgres)
                ↓
         Evaluation Loop (LangSmith)

### Proyecto Clave: Tu Primer Agente Comercializable

**Ejemplo concreto:** Agente de "Prospectación y Primera Respuesta" para consultoras B2B:

1. **Input:** URL de LinkedIn de una empresa objetivo
    
2. **Proceso:**
    
    - Scraping de web + LinkedIn
        
    - Análisis de necesidades (LLM)
        
    - Redacción de email personalizado
        
    - Envío automático (integración Gmail/Outlook)
        
3. **Evaluación:** Métricas de respuesta, no solo "funciona"
    

### Checkpoint

- Tu agente maneja 3 tools diferentes
    
- Tiene memoria de conversación persistente
    
- Puedes trazar (trace) exactamente qué hizo paso a paso (observability)
    

---

## 🚀 FASE 5: Producción y Venta (Semanas 43-54)

**Lo técnico es 40%; el 60% es producto, legal y operaciones.**

### Aspectos Técnicos de Producción

1. **Deployment:** FastAPI + Docker + AWS/GCP (Aprende [aquí](https://github.com/Running-Fast-and-Slow/AI-Engineering))
    
2. **Costos:** Optimización de tokens (prompt caching, modelos pequeños para tareas simples)
    
3. **Seguridad:** PII masking, rate limiting, guardrails (uso de [NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) o similar)
    
4. **Monitoreo:** LangSmith o Langfuse para ver fallos en tiempo real
    

### Aspectos Comerciales (Paralelo desde la semana 40)

- **Semanas 40-44:** Validación con 3 beta testers gratuitos. Recoge feedback brutalmente honesto.
    
- **Semanas 45-48:** Legal: Términos de servicio, política de privacidad (especialmente si procesas datos de terceros), considerar constituir empresa (SAS/SL en España/LATAM, LLC en US).
    
- **Semanas 49-52:** Pricing: No por hora, por valor. Si tu agente ahorra 10h/semana a un consultor, cobra 200-300€/mes.
    
- **Semana 54:** Primer pago real.
    

---

## 📅 Timeline Realista de Tu Primer Ingreso

| Momento    | Qué Tendrás                                       | Estado Legal/Venta                        |
| ---------- | ------------------------------------------------- | ----------------------------------------- |
| **Mes 6**  | Agente que funciona en tu laptop                  | Tests internos                            |
| **Mes 9**  | Agente desplegado en cloud, 3 usuarios beta       | Validación de mercado                     |
| **Mes 12** | Producto con evaluaciones automáticas, estable    | **Primer cliente pagando (200-500€/mes)** |
| **Mes 18** | Multi-tenant, onboarding automático, 10+ clientes | Ingresos recurrentes establecidos         |

## ⚠️ Advertencias Críticas (Para No Perder Tiempo)

1. **No uses ChatGPT para aprender código:** Escribe cada línea tú mismo los primeros 6 meses. La "velocidad" de copiar te hará lento para debuggear en producción.
    
2. **El tutorial trap:** Después de cada tutorial, ciérralo y reescríbelo desde cero sin mirar. Si no puedes, no entendiste.
    
3. **Framework hopping:** No saltes de LangChain a CrewAI a AutoGen porque uno "no funciona". Domina uno primero (recomiendo LangGraph para arquitectura compleja, CrewAI para empezar rápido).
    
4. **El mito del "agente general":** Tu primer agente comercial debe hacer **una sola cosa** muy bien (ej: "responder emails de soporte nivel 1 para tiendas Shopify"), no "hacer cualquier tarea".
    
5. **Costos ocultos:** Un agente usando GPT-4o puede costarte 0.10€ por conversación. Si tienes 100 clientes usando 50 conversaciones/día, son 15,000€/mes solo en APIs. Aprende a usar modelos locales (Llama 3.3, Qwen) para tareas simples.
    

---

## 📚 Tu Biblioteca Open Source Definitiva

Guarda estos repositorios (son el estándar oro en 2025):

1. **Fundamentos:** [microsoft/ML-For-Beginners](https://github.com/microsoft/ML-For-Beginners)
2. **Deep Learning:** [karpathy/nn-zero-to-hero](https://github.com/karpathy/nn-zero-to-hero)
3. **MLOps:** [GokuMohandas/Made-With-ML](https://github.com/GokuMohandas/Made-With-ML)
4. **LLMs:** [HandsOnLLM/Hands-On-LLMs](https://github.com/HandsOnLLM/Hands-On-LLMs)
5. **Agentes:** [microsoft/AI-Agents-for-Beginners](https://github.com/microsoft/AI-Agents-for-Beginners) (lanzado en 2025, muy actualizado)



---
#### Referencias

