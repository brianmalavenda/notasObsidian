tipo:: #mpg/empresas 
tags::
fecha:: 2025-12-12
links::
links_externos::

# Moore Threads Open-Sources TileLang-MUSA reduce el volumen del código en un 90%
11/2/2026

El fabricante chino de GPU Moore Threads anunció el 30 de enero de 2026 que ha abierto el código fuente del proyecto TileLang-MUSA, brindando soporte completo para el lenguaje de programación TileLang en su arquitectura de GPU. La compañía reiteró el anuncio el 10 de febrero.

Según Moore Threads, el proyecto ha sido validado en múltiples generaciones de sus GPU con todas las funciones y tiene como objetivo reducir las barreras de desarrollo a través de abstracciones de alto nivel y optimizaciones del compilador, ofreciendo herramientas eficientes de desarrollo de IA y computación de alto rendimiento (HPC) para plataformas informáticas nacionales.

TileLang es un lenguaje de programación de operadores de IA de alto rendimiento basado en la abstracción de mosaicos tensoriales. Con sintaxis declarativa y una interfaz similar a Python, permite a los desarrolladores describir la intención computacional en una forma cercana a las expresiones matemáticas. El lenguaje está diseñado en torno a tres capacidades principales: reducir la barrera de entrada a través de abstracciones de alto nivel, permitir la portabilidad multiplataforma “escribir una vez, ejecutar en múltiples arquitecturas” y delegar tareas complejas —como optimización de bucles y programación de memoria— al compilador.

TileLang ya se ha utilizado en el desarrollo de modelos a gran escala como DeepSeek-V3 y tiene aplicaciones en computación científica y desarrollo de hardware.

El nuevo proyecto de código abierto TileLang-MUSA se centra en liberar el potencial de rendimiento de las GPU nacionales. Ha sido validado en tarjetas aceleradoras integradas de entrenamiento e inferencia Moore Threads’ MTT S5000 y MTT S4000. El proyecto logra un mapeo profundo entre la semántica de alto nivel de TileLang y la arquitectura MUSA de Moore Threads’, admitiendo la invocación automática de instrucciones de núcleo tensorial, optimización del movimiento de datos multinivel y procesamiento paralelo a nivel de deformación. La cobertura de pruebas unitarias de operadores nativos actualmente supera el 80%, lo que proporciona una base de desarrollo estable.

Los puntos de referencia de rendimiento muestran que al desarrollar operadores clave para grandes modelos de lenguaje utilizando TileLang-MUSA, los desarrolladores pueden reducir el volumen de código en aproximadamente un 90% en comparación con las implementaciones MASA C++ escritas a mano. En escenarios de cálculo matricial, el rendimiento alcanza hasta el 95% de las versiones optimizadas manualmente, mientras que los operadores de mecanismos de atención alcanzan alrededor del 85%. Su mecanismo de ajuste automático puede buscar rápidamente estrategias de mosaico óptimas, lo que permite obtener ganancias de rendimiento más allá de las líneas de base no optimizadas.

El proyecto permite a los desarrolladores migrar sin problemas la lógica del operador existente a plataformas GPU nacionales y proporciona una interfaz de desarrollo de alto nivel para ingenieros que no están familiarizados con instrucciones de bajo nivel.

Moore Threads dijo que planea continuar optimizando el rendimiento del compilador, profundizar la integración con los principales marcos de IA y ampliar el soporte a la optimización global para arquitecturas de modelos complejos como Transformers.

Fuente: IT Home

# 2025

La búsqueda de la autosuficiencia tecnológica de Pekín tiene un nuevo rey: Moore Threads, el diseñador de chips, ha protagonizado un debut bursátil histórico en Shangái. Sus acciones se dispararon más de un 500% en su primer día de cotización. 

Fue fundada en 2020 por Zhang Jianzhong, quien fuera gerente general de NVIDIA en China. De hecho, este conocimiento interno es lo que llevó a EEUU a considerarla una amenaza directa e incluirla en su lista negra en 2023.

La euforia ha validado la estrategia de un gigante que, a pesar de estar en la lista negra de EEUU, se ha convertido en una de las grandes esperanzas para romper el bloqueo de semiconductores. Y en esta maniobra, el gran beneficiado ha sido el fundador de DeepSeek.

Nikkei Asia confirma que la empresa ha alcanzado una capitalización de 305.000 millones de yuanes (unos 42.000 millones de dólares), convirtiéndose en la cuarta empresa más valiosa del mercado STAR. Y eso que todavía no es rentable: espera serlo en 2027.

Sus GPUs, como la MTT S4000, son la punta de lanza de una industria que busca sustituir a las H100 y H200 —esta última sí llegará a China directamente— americanas en los centros de datos estatales, donde el gobierno ya exige una cuota del 50% de chips locales para estos equipos tan cruciales.

La startup china ha desarrollado MUSA, una plataforma que [permite reciclar código escrito para NVIDIA](https://www.xataka.com/robotica-e-ia/china-ha-dado-paso-gigante-tiene-alternativa-a-cuda-nvidia-para-ia-que-permite-reciclar-su-codigo) y ejecutarlo en sus propias GPUs.

---
#### Referencias

