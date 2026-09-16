tipo::
tags::
fecha:: 2026-09-11
links::
links_externos::


Y lograr que tras hospedar modelos ya entrenados (como _Llama_ de Meta o _Mistral_) que sean de código abierto. Entrenar un modelo desde cero con datos argentinos (modismos, leyes, historia local). Esto requiere una inversión en hardware que hoy Argentina suele tercerizar o realizar en colaboración con universidades como la UBA o el Conicet, que tienen supercomputadoras (como la _Tupac_ o la _Clementina XXI_). Con esto se garantizaría que cada habitante del país en la interacción con la IA local genere el entrenamiento y reaprendizaje constante del modelo además de asegurar que este “dialogo” con la herramienta de IA quede en el terreno local.

_Clementina XXI_ esta instalada en el Centro de Cómputos del Servicio Meteorológico Nacional (SMN) e inaugurada recientemente bajo el ala del Ministerio de Ciencia y Tecnología y el CONICET, es **una de las supercomputadoras más potentes de América Latina**. A diferencia de las supercomputadoras más viejas que solo usaban procesadores tradicionales (CPU), Clementina XXI fue diseñada con **aceleradores gráficos (GPUs NVIDIA)** de alta gama. Esto la convierte en la candidata ideal para el entrenamiento y ajuste fino (_fine-tuning_) de modelos de IA con millones de parámetros, utilizando textos históricos, leyes argentinas, digitalizaciones de la UBA y registros culturales.

Para coordinar que los algoritmos de la UBA corran sobre los fierros de la supercomputadora Clementina XXI y terminen empaquetados en el Data Center de ARSAT, se necesitan ingenieros de sistemas a gran escala.

[https://www.prensa.unlu.edu.ar/?q=node/10679](https://www.prensa.unlu.edu.ar/?q=node/10679)

[https://www.argentina.gob.ar/ciencia/infraestructuras-de-investigacion/sistemas-nacionales/clementina-xxi](https://www.argentina.gob.ar/ciencia/infraestructuras-de-investigacion/sistemas-nacionales/clementina-xxi)

El factor determinante para saber si un Large Language Model (LLM) cabe en una supercomputadora es la Memoria de Video (VRAM) y la RAM del sistema. Para ejecutar un modelo de aproximadamente 748.000 millones de parámetros, el consumo de memoria depende de la precisión (cuantización) que se utilice. En precisión completa (16 bits / FP16): Cada parámetro ocupa 2 bytes. El modelo base requiere al menos 1.500 GB (1.5 Terabytes) de VRAM solo para cargar los pesos, sin contar el espacio necesario para el contexto de la conversación (KV Cache)

(Cuantización a 4 bits / Q4): Es el estándar técnico para ahorrar recursos. Reduce el peso a unos 400–450 GB de VRAM perdiendo menos del 1% de calidad.

Aceleradoras: 296 GPUs Intel Data Center Max 1550 (arquitectura Ponte Vecchio) distribuidas en 74 nodos.

VRAM por GPU: Cada tarjeta cuenta con 128 GB de memoria de muy alto ancho de banda (HBM2e).

Memoria total en GPUs: 296 x 128 = 37.888 GB (aprox. 38 TB) de VRAM

Red de interconexión: InfiniBand de 400 Gbps, fundamental para que las GPUs compartan información a velocidades ultra altas.

Aunque Clementina XXI tiene memoria de sobra para ejecutarlo, el desafío no es el tamaño, sino el ecosistema de software. La enorme mayoría de los modelos de DeepSeek y las librerías de IA actuales están optimizados nativamente para la arquitectura Nvidia CUDA. Ejecutar un modelo de esta escala en el hardware Intel Max (Ponte Vecchio) de Clementina XXI requiere adaptar el código utilizando el ecosistema Intel oneAPI / SYCL, un trabajo de ingeniería de software complejo pero totalmente posible

**CSC (Centro de Simulación Computacional - CONICET):** Ubicado en el Polo Científico Tecnológico, este centro domina la Computación de Alto Desempeño (HPC). Aunque históricamente modelaban física o fluidos, sus ingenieros tienen el _know-how_ técnico para exprimir la capacidad de supercomputadores como _Tupac_ o la infraestructura del Servicio Meteorológico.

**CID (Centro Interinstitucional de Ciencia de Datos):** Articula proyectos de datos de alto impacto entre múltiples disciplinas y universidades, funcionando como el puente perfecto para la transferencia tecnológica hacia organismos estatales.

**LIAA (Laboratorio de Inteligencia Artificial Aplicada - UBA):** Ubicado en el Pabellón "0+Infinito" de Ciudad Universitaria, este centro (dependiente del Departamento de Computación de la Facultad de Ciencias Exactas y Naturales) es el líder indiscutido. Científicos como Diego Fernández Slezak y su equipo investigan la estructura interna de los Grandes Modelos de Lenguaje (LLM).

**Instituto de Cálculo "Rebeca Cherep de Guber" (CONICET - UBA):** El Co-Laboratorio de Ciencia de Datos e IA, liderado por investigadoras como Laura Ación, está especializado en el **uso responsable y ético de datos**. Su aporte es crucial para auditar que el modelo nacional no reproduzca sesgos discriminatorios y que la privacidad de los datos de entrenamiento esté blindada.

**Pladema (Universidad Nacional del Centro - UNICEN, Tandil):** Un histórico polo tecnológico que, en conjunto con el CONICET, desarrolla optimización de algoritmos y computación gráfica de alto rendimiento. Tienen una larga trayectoria transfiriendo desarrollos de software complejo hacia el sector público y privado.

## infraestructura digital

**¿Con qué "soberanía de hardware" contamos?**

A nivel de fabricación de silicio o hardware de red complejo, **Argentina no produce equipamiento propio**; depende 100% de la importación de estos componentes. Si el proyecto de centralización y control que veníamos trazando se pusiera en marcha, el país se enfrentaría a un dilema geopolítico:

- Para la **capa algorítmica e intelectual** (la materia gris), tenemos la UBA, el CONICET e INVAP.
- Pero para los **"fierros"** que ejecutan esa lógica (los switches y cortafuegos masivos), Argentina tendría que decidir si financiar su infraestructura comprándole tecnología de inspección a **China** (que ya tiene los equipos con funciones de filtrado masivo integradas de fábrica) o seguir utilizando la arquitectura tradicional **estadounidense**, asumiendo que los sistemas de seguridad occidentales responderían a las directrices del Estado local.

**CEATSA (soberanía espacial)**

CEATSA es una Sociedad Anónima constituida en 2010 por dos socios estatales principales: **ARSAT** y **INVAP**. Esto significa que ARSAT es uno de los dueños de la infraestructura de CEATSA. El vínculo con ARSAT permitió que Argentina cerrara el ciclo de fabricación satelital en territorio nacional. Se trata de un laboratorio de alta complejidad ubicado en **San Carlos de Bariloche**. Su función primordial es realizar pruebas de confiabilidad sobre sistemas complejos antes de que operen en entornos críticos. CEATSA cuenta con infraestructura única en el Cono Sur:

- **Cámaras Termo-Vacío:** Simulan las condiciones de presión y temperatura del espacio exterior.
- **Ensayos de Vibración (Shaker):** Replican las fuerzas mecánicas y vibraciones violentas que ocurren durante el lanzamiento de un cohete.
- **Cámaras Anecoicas:** Espacios blindados para medir la compatibilidad electromagnética y el rendimiento de antenas sin interferencias externas.
- **Ensayos Acústicos:** Prueban la resistencia de los componentes ante el ruido extremo generado por los motores de los lanzadores.

Aunque el vinculo con las pruebas de satelites es lo más destacado de la compañia tambien brinda servicios a la industria **automotriz** (ensayos de fatiga y clima), **energética** (evaluación de componentes de aerogeneradores) y **defensa** (radares y sistemas de comunicación).

**Hito ARSAT-1 y ARSAT-2:** El vínculo se consolidó con las pruebas de los primeros satélites de telecomunicaciones geoestacionarios argentinos. Ambos satélites pasaron meses en los laboratorios de CEATSA superando pruebas de vibración y vacío antes de ser enviados a la Guayana Francesa para su lanzamiento. Mientras ARSAT se encarga del diseño de la misión y la operación de los satélites en órbita, CEATSA actúa como el "banco de pruebas" que certifica que el hardware diseñado por ARSAT (e integrado por INVAP) funcionará correctamente en el vacío del espacio.

**INVAP**

**INVAP** (Investigaciones Aplicadas) es una empresa de tecnología de vanguardia, propiedad de la provincia de Río Negro, que se ha consolidado como el máximo referente de la ingeniería compleja en Argentina y un actor relevante en el mercado tecnológico global. INVAP no se dedica a una sola rama, sino que diseña y construye sistemas tecnológicos complejos en sectores estratégicos:

Nuclear: Es su origen y mayor fortaleza. Diseñan y construyen reactores nucleares de investigación y producción de radioisótopos (para uso medicinal). Han exportado esta tecnología a países como Egipto, Argelia, Perú, Australia (el reactor OPAL) y recientemente a Países Bajos (el reactor PALLAS).  
Espacial: Son los responsables de la ingeniería, integración y construcción de los satélites de ARSAT (telecomunicaciones) y de la serie SAOCOM (observación de la Tierra con radar de microondas). Defensa y Seguridad: Desarrollan radares primarios y secundarios para el control del tráfico aéreo civil y la vigilancia de fronteras (Serie RPA). También trabajan en sistemas de defensa y drones (VANT). Sistemas Médicos: Fabrican equipamiento para radioterapia y medicina nuclear (centros de terapia radiante).

**1. La Capa de Acceso (OLTs y ONTs)**

Esta es la tecnología que "ilumina" la fibra óptica hacia los hogares y empresas (el equipamiento de última milla). Aquí **China tiene un dominio casi absoluto** debido a sus costos competitivos y agresiva financiación durante la última década.

- **Marcas dominantes:** **Huawei** y **ZTE**.
- **Presencia nacional:** La gran mayoría de las cooperativas locales y PyMEs ISP que se conectan a los nodos de ARSAT utilizan OLTs chinas. En el mercado residencial, las ONTs (los módems de fibra que están en las casas de los usuarios) son en un 85-90% de origen chino.
- **Alternativas occidentales:** Existe equipamiento de **Nokia** (origen europeo/finlandés) y **Calix** o **Adtran** (EE. UU.), utilizado principalmente por grandes operadores (como Telecom o Claro) en nodos troncales de acceso, pero en menor volumen que las marcas asiáticas.

**2. El Núcleo de Red y Enrutamiento (Routers de Core y Switches de IXP)**

En el "corazón" de internet, donde se decide hacia dónde van los paquetes a nivel mayorista (los nodos centrales de ARSAT y los switches principales de los IXP), la balanza está muy repartida entre **Estados Unidos y China**.

- **Líderes Estadounidenses:** **Cisco Systems** y **Juniper Networks**. Tradicionalmente, la arquitectura de internet en Argentina se construyó sobre Cisco. ARSAT y los IXP más grandes (como el de Buenos Aires) tienen un porcentaje altísimo de routers de core de estas marcas debido a su estabilidad y soporte de protocolos complejos (como BGP o MPLS).
- **El avance de China:** **Huawei** ha ganado un terreno enorme en el transporte mayorista y nodos de agregación intermedios. Gran parte de las ampliaciones de la REFEFO de los últimos años incorporaron tecnología de transporte óptico (DWDM) de origen chino.

**3. Seguridad y Cortafuegos (Firewalls Perimetrales)**

Para la capa de ciberseguridad (los equipos encargados de mitigar ataques y que, en el hipotético escenario que discutíamos, harían la inspección profunda), el mercado corporativo y estatal argentino es **abrumadoramente estadounidense**.

- **Marcas principales:** **Fortinet**, **Palo Alto Networks** y **Check Point** (origen israelí, pero con fuerte base en EE. UU.).
- **Disponibilidad:** El Data Center de ARSAT en Benavídez y los entornos de nube gubernamentales se protegen principalmente con appliances físicos y lógicos de estas marcas. El software y los chips ASIC de estos equipos están diseñados específicamente para procesar reglas de firewall a nivel de hardware, pero su propiedad intelectual y actualizaciones dependen directamente de casas matrices en el exterior.

|**Tipo de Equipo**|**Función Clave**|**Origen Dominante**|**Marcas Principales en el País**|
|---|---|---|---|
|**Routers de Core / Switches IXP**|Enrutamiento masivo|🇺🇸 EE. UU. / 🇨🇳 China|Cisco, Juniper, Huawei|
|**OLTs / ONTs**|Distribución de Fibra (Última milla)|🇨🇳 China|Huawei, ZTE, Nokia|
|**Cortafuegos / Firewalls**|Seguridad e Inspección|🇺🇸 EE. UU. / 🇮🇱 Israel|Fortinet, Palo Alto, Check Point|
|**Sistemas Ópticos (DWDM)**|Iluminar los miles de km de fibra|🇨🇳 China / 🇫🇷 Europa|Huawei, Nokia (ex Alcatel-Lucent)|

**El Socio Local: ¿Quién tiene la capacidad de producir los "Fierros"?**

Aquí es donde debemos separar la **ingeniería de diseño** de la **fabricación en serie**.

**El rol de INVAP: El Diseñador, no la Fábrica**

Como bien suponías, **INVAP** es lo más avanzado que tenemos, pero con un matiz importante: **INVAP es una casa de alta ingeniería de diseño complejo, no una planta de producción masiva de electrónica de consumo**.

- **Su fuerte:** INVAP tiene la capacidad de diseñar placas de circuito integrado de alta complejidad, sistemas embebidos, aviónica para satélites y la lógica de radares.
- **En el Joint Venture:** INVAP sería el socio ideal para tomar el software y la arquitectura china y "argentinizarla", auditando el código fuente para asegurar que no existan puertas traseras (_backdoors_) extranjeras y adaptando los protocolos a las necesidades del Estado y de ARSAT.

**La verdadera "Fábrica": Pixart (La planta inteligente)**

Si buscamos quién tiene las máquinas para soldar componentes y ensamblar placas a nivel industrial 4.0 hoy en el país, la respuesta es **Pixart**.

- **Qué es:** Ubicada en el Parque Industrial de Escobar, es la primera "fábrica inteligente" automatizada y robótica de Latinoamérica.
- **Su capacidad:** Cuenta con alianzas para ensamblar componentes complejos y microchips de avanzada (trabajando en conjunto con firmas globales). Tienen líneas de producción automatizadas operadas por brazos robóticos capaces de fabricar hardware a gran escala.
- **En el Joint Venture:** Si ARSAT e INVAP diseñan el Cortafuegos Nacional junto al socio chino, **Pixart** es la que tiene la capacidad física real en el territorio para fabricar las placas madre, soldar los procesadores y ensamblar los _racks_ perimetrales que luego se instalarían en los IXP.

**El esquema ideal para el Joint Venture**

Para revivir el espíritu de FATE pero con la tecnología actual, el consorcio de soberanía en hardware debería estructurarse de la siguiente manera:

1. **Socio Tecnológico (Huawei / Sangfor):** Transfiere la tecnología de los chips de red y el sistema operativo base del firewall.
2. **INVAP:** Modifica el diseño, audita la ciberseguridad del firmware y asegura que el control lógico de las llaves de cifrado quede en manos argentinas.
3. **Pixart (o Mirgor):** Actúa como la planta de fabricación local, imprimiendo las placas y ensamblando el hardware físico bajo normas de calidad industrial.
4. **ARSAT:** El cliente ancla que compra estos equipos para nacionalizar los IXP y la REFEFO.

**Ranking de ISPs en linea fija Argentina (Por Volumen de Clientes)**

El mercado de internet fijo en Argentina cuenta con aproximadamente **11.5 millones de accesos**. La distribución de cuota de mercado se concentra de la siguiente manera:

---
#### Referencias

