tipo::
tags::
fecha:: 2026-09-08
links::
links_externos::

# PROPUESTA DE PROYECTO DE INVESTIGACIÓN Y DESARROLLO

**Título del Proyecto:** Arquitectura de Inteligencia Artificial Policéntrica y Descentralizada para la Red Federal de Conocimiento Académico y Científico **Área:** Sistemas Distribuidos, Criptografía, Inteligencia Artificial y Gobernanza Digital **Destinatarios:** Consejo Superior / Comités de Investigación y Desarrollo Tecnológico Universitario

---

## 1. RESUMEN EJECUTIVO

El paradigma actual de la Inteligencia Artificial está dominado por un esquema monocéntrico y hegemónico: grandes corporaciones concentran la capacidad de cómputo y los conjuntos de datos en supercentros de datos. Este enfoque genera tres problemas estructurales:

1. **Aplastamiento de la diversidad cultural y científica:** La IA centralizada opera mediante promedios estadísticos donde las particularidades regionales, biomas locales y saberes de nicho son homogenizados por la masa de datos mayoritaria.
2. **Opacidad e inviabilidad de auditoría:** Los Grandes Modelos de Lenguaje (LLMs) comerciales actúan como "cajas negras" sin trazabilidad de fuentes, propensos a alucinaciones y datos no auditados.
3. **Pérdida de soberanía del conocimiento:** Las instituciones de educación superior actúan como meras consumidoras de modelos empaquetados, cediendo su producción intelectual a terceros.

Esta propuesta presenta la **IA Policéntrica**, un marco tecnológico y conceptual donde la red no posee un centro único, sino múltiples nodos centrales institucionales (universidades e institutos de investigación). Mediante el consenso criptográfico y la descentralización del almacenamiento, se garantiza la veracidad de la información y la soberanía de los datos, permitiendo que la inteligencia colectiva se desarrolle de manera orgánica sin invisibilizar las particularidades locales.

---

## 2. MARCO CONCEPTUAL: EL PARADIGMA POLICÉNTRICO

El concepto de Policentrismo postula que un sistema complejo puede operar con múltiples centros de decisión autónomos que se coordinan mediante reglas compartidas.

```text
┌─────────────────────────────────────────┐

│ RED DE CONSENSO Y GOBERNANZA │

│ (Blockchain Permisionada Académica) │

└────────────────────┬────────────────────┘

│

┌─────────────────────┼─────────────────────┐

▼ ▼ ▼

┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐

│ NODO POLICÉNTRICO│ │ NODO POLICÉNTRICO│ │ NODO POLICÉNTRICO│

│ UNIVERSIDAD A │ │ UNIVERSIDAD B │ │ UNIVERSIDAD C │

├─────────────────┤ ├─────────────────┤ ├─────────────────┤

│ • Bioma Local │ │ • Cs. Sociales │ │ • Biotecnología │

│ • dRAG Local │ │ • dRAG Local │ │ • dRAG Local │

│ • Swarm Learning│ │ • Swarm Learning│ │ • Swarm Learning│

└────────┬────────┘ └────────┬────────┘ └────────┬────────┘

│ │ │
```

[Red P2P Term.] [Red P2P Term.] [Red P2P Term.]

**Principios Fundamentales**

- **No Reducción de la Particularidad:** La centralidad de la red no niega la identidad de cada nodo. Si una universidad se especializa en la flora de un bioma autóctono, su base de conocimiento local preserva su peso epistémico absoluto.
- **Agregación por Consenso, no por Absorción:** El conocimiento global se genera orquestando consultas a los nodos especialistas bajo certificación de veracidad, no "mezclando" arbitrariamente los pesos de una red neuronal.
- **Soberanía de Datos Institucionales:** Cada universidad conserva la custodia física de sus investigaciones y decide los niveles de visibilidad mediante pruebas criptográficas de cero conocimiento (zk-SNARKs).

---

## 3. ARQUITECTURA TÉCNICA DETALLADA

El sistema se divide en cuatro capas tecnológicas integradas, optimizadas según los estándares de computación federada y redes distribuidas a gran escala:

```text
┌─────────────────────────────────────────────────────────────────────────┐

│ CAPA 4: INTERFAZ DE WEB SOBERANA (dApp de Consulta y Publicación) │

├─────────────────────────────────────────────────────────────────────────┤

│ CAPA 3: MOTOR DE IA DESCENTRALIZADO (dRAG + SWARM LEARNING) │

├─────────────────────────────────────────────────────────────────────────┤

│ CAPA 2: CONSENSO Y GOBERNANZA ON-CHAIN (BLOCKCHAIN PERMISIONADA - PoA) │

├─────────────────────────────────────────────────────────────────────────┤

│ CAPA 1: ALMACENAMIENTO DISTRIBUIDO Y RED P2P (IPFS / DHT / LIBP2P) │

└─────────────────────────────────────────────────────────────────────────┘
```

### Capa 1: Almacenamiento Distribuido Off-Chain (Red P2P)

Los documentos científicos no se almacenan en la blockchain. Se utiliza una red P2P integrada por Nodos Terminales operados por las facultades.

- **Direccionamiento por Contenido (CID):** Identificadores únicos basados en hash criptográfico SHA-256 / Blake3.
- **Fragmentación y Redundancia:** Uso de protocolo IPFS / Libp2p con Códigos de Borrado (Erasure Coding) para reconstruir documentos aun cuando nodos estén offline.
- **Optimización de Latencia (Caching):** Basado en lecciones aprendidas de redes P2P a gran escala en producción, se integrará una capa de caché (ej. Redis) sobre IPFS para acelerar la recuperación de CIDs frecuentemente consultados, evitando cuellos de botella en la dApp.
- **Protección de Fuentes:** Enrutamiento por capas (Mixnets / Onion Routing) en Libp2p para ocultar la IP del investigador origen.

### Capa 2: Consenso y Gobernanza On-Chain (Blockchain Validadora)

Capa de confianza conformada por Nodos Ledger Validadores administrados por los centros de cómputo de cada universidad.

- **Mecanismo de Consenso:** Proof of Authority (PoA) / IBFT 2.0. Sin consumo energético innecesario; la validación se basa en la identidad digital permisionada de las instituciones.
- **Infraestructura PKI y Certificados X.509:** Atestación criptográfica emitida por la autoridad de la red interuniversitaria.
- **Gobernanza de Admisión:** La inclusión de nuevos documentos exige la firma del quórum mediante Smart Contracts.

### Capa 3: Motor de IA Descentralizado (dRAG y Swarm Learning)

El conocimiento se organiza mediante _Decentralized Retrieval-Augmented Generation_ (dRAG) y _Swarm Learning_.

```text

[Consulta del Usuario en dApp]

│

▼

┌─────────────────────────────────────────┐

│ CAPA DE ABSTRACCIÓN DE INFERENCIA │

│ (LLM Base Neutro / Open Source) │

└─────────────────────┬───────────────────┘

│ (Búsqueda Vectorial Paralela)

┌──────────────┼──────────────┐

▼ ▼ ▼

┌─────────────┐┌─────────────┐┌─────────────┐

│ dRAG Node ││ dRAG Node ││ dRAG Node │

│ Univ. A ││ Univ. B ││ Univ. C │

└──────┬──────┘└──────┬──────┘└──────┬──────┘

└──────────────┼──────────────┘

│ (Respuesta Sintetizada con Citas Certificadas)
```

**A. dRAG (Decentralized Retrieval-Augmented Generation)**

1. **Indexación Vectorial Local:** Cada nodo mantiene un índice de vectores de sus documentos utilizando un modelo de embedding estandarizado multilingüe (ej. `bge-m3` de la Beijing Academy of AI), garantizando procesamiento de diversidad cultural y lingüística.
2. **Búsqueda Vectorial Paralela:** La dApp envía el vector de pregunta a los nodos especialistas relevantes.
3. **Trazabilidad Inmutable:** Cada fragmento inyectado al LLM lleva adjunto su CID y la dirección del Smart Contract validador. La respuesta final cita fuentes verificables on-chain.

**B. Swarm Learning (Aprendizaje en Enjambre)** Para adaptar pesos neuronales sin centralizar datos:

1. Cada universidad entrena un adaptador liviano (LoRA) exclusivamente con sus datos locales.
2. **Optimización Computacional Off-Chain:** Siguiendo estándares de producción de Computación Federada (ej. proyecto FATE), el promedio de los parámetros LoRA se ejecuta en servidores de cómputo efímeros delegados por consenso.
3. **Verificación On-Chain:** El Smart Contract _no_ ejecuta el promediado (para no sobrecargar la blockchain), sino que solo verifica la firma criptográfica (zk-SNARK) del modelo agregado para registrar su inmutabilidad y evitar manipulación.

### Capa 4: Aplicación Descentralizada (dApp) y Web Soberana

Interfaz de acceso para la comunidad científica que combina simplicidad web con seguridad descentralizada.

- **Frontend Resiliente:** La aplicación web se distribuye vía IPFS, eliminando riesgos de censura o caídas de servidores centrales.
- **Gateway de Accesibilidad HTTP:** Dado que las universidades suelen tener firewalls estrictos, se dispondrá de un puente HTTP tradicional hacia el backend on-chain. Esto permite que los evaluadores y docentes accedan sin fricción (sin necesidad de wallets o extensiones cripto), manteniendo la integridad de la red.
- **Intercambiabilidad de Modelos:** La capa de inferencia se conecta vía estándares abiertos (vLLM / LiteLLM), permitiendo sustituir el LLM base (ej. pasando de Llama a DeepSeek o Qwen) sin alterar la base vectorial ni el histórico de la blockchain.
- **Autenticación zk:** Los investigadores firman aportes acreditando su membresía sin revelar su nombre o ubicación física.

---

## 4. PLAN DE IMPLEMENTACIÓN Y FASES DEL PROYECTO

El desarrollo se estructura en cuatro fases de seis meses cada una (24 meses en total):

|Fase|Hito Principal|Entregables Técnicos|
|---|---|---|
|**Fase 1**  <br>(Meses 1-6)|Red Base y Gobernanza|Despliegue de testnet de Blockchain PoA con 3 nodos validadores piloto. Configuración de PKI y Smart Contracts de registro.  <br>**Piloto Específico:** Interconexión del repositorio de tesis de la Facultad de Ciencias (Univ. A) con el laboratorio de Biotecnología (Univ. B) para validar dRAG sin compartir PDFs originales.|
|**Fase 2**  <br>(Meses 7-12)|Almacenamiento P2P y dRAG|Integración de IPFS/Libp2p + Caching. Desarrollo del pipeline de vectorización con `bge-m3` y motor dRAG inicial.|
|**Fase 3**  <br>(Meses 13-18)|Swarm Learning e Inferencia|Implementación de orquestación de LoRA Off-Chain con verificación On-Chain. Integración de la capa de inferencia con modelos open-source.|
|**Fase 4**  <br>(Meses 19-24)|Portal dApp y Producción|Lanzamiento de la dApp pública con Gateway HTTP. Auditoría de seguridad de Smart Contracts y expansión a nuevos nodos universitarios.|

---

## 5. IMPACTO ACADÉMICO Y VIABILIDAD INSTITUCIONAL

La aprobación de este proyecto posicionará a las universidades participantes en la vanguardia de la Inteligencia Artificial Descentralizada (DeAI), ofreciendo beneficios concretos:

1. **Soberanía Tecnológica:** Eliminación de la dependencia de licencias corporativas de IA y control total sobre el destino de la producción científica.
2. **Garantía Anti-Alucinación:** Creación del primer ecosistema de IA académica con validación matemática de fuentes y rigor bibliográfico inmutable.
3. **Protección del Patrimonio:** Cada región o universidad preserva el peso de sus investigaciones locales, evitando la hegemonía de las agendas científicas dominantes.
4. **Viabilidad Económica y Eficiencia de Infraestructura:** Al emplear el hardware existente en los servidores de las facultades y modelos de IA abiertos y eficientes (ej. DeepSeek, Qwen), el costo del proyecto se reduce al desarrollo de software y la coordinación interinstitucional, evitando la dependencia de costosas infraestructuras cloud (AWS/Google Cloud) y democratizando el acceso a la tecnología.


---
#### Referencias

