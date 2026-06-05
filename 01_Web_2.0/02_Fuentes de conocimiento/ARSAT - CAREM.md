tipo::
tags::
fecha:: 2026-06-02
links::
links_externos:: https://www.youtube.com/watch?v=bG1rgnCcvZw


## CEATSA (soberanía espacial)

CEATSA es una Sociedad Anónima constituida en 2010 por dos socios estatales principales: **ARSAT** y **INVAP**. Esto significa que ARSAT es uno de los dueños de la infraestructura de CEATSA.  El vínculo con ARSAT permitió que Argentina cerrara el ciclo de fabricación satelital en territorio nacional. Se trata de un laboratorio de alta complejidad ubicado en **San Carlos de Bariloche**. Su función primordial es realizar pruebas de confiabilidad sobre sistemas complejos antes de que operen en entornos críticos. CEATSA cuenta con infraestructura única en el Cono Sur: 
- **Cámaras Termo-Vacío:** Simulan las condiciones de presión y temperatura del espacio exterior.
- **Ensayos de Vibración (Shaker):** Replican las fuerzas mecánicas y vibraciones violentas que ocurren durante el lanzamiento de un cohete.
- **Cámaras Anecoicas:** Espacios blindados para medir la compatibilidad electromagnética y el rendimiento de antenas sin interferencias externas.
- **Ensayos Acústicos:** Prueban la resistencia de los componentes ante el ruido extremo generado por los motores de los lanzadores.

Aunque el vinculo con las pruebas de satelites es lo más destacado de la compañia tambien brinda servicios a la industria **automotriz** (ensayos de fatiga y clima),  **energética** (evaluación de componentes de aerogeneradores) y **defensa** (radares y sistemas de comunicación).

**Hito ARSAT-1 y ARSAT-2:** El vínculo se consolidó con las pruebas de los primeros satélites de telecomunicaciones geoestacionarios argentinos. Ambos satélites pasaron meses en los laboratorios de CEATSA superando pruebas de vibración y vacío antes de ser enviados a la Guayana Francesa para su lanzamiento. Mientras ARSAT se encarga del diseño de la misión y la operación de los satélites en órbita, CEATSA actúa como el "banco de pruebas" que certifica que el hardware diseñado por ARSAT (e integrado por INVAP) funcionará correctamente en el vacío del espacio.


## INVAP

**INVAP** (Investigaciones Aplicadas) es una empresa de tecnología de vanguardia, propiedad de la provincia de Río Negro, que se ha consolidado como el máximo referente de la ingeniería compleja en Argentina y un actor relevante en el mercado tecnológico global. INVAP no se dedica a una sola rama, sino que diseña y construye sistemas tecnológicos complejos en sectores estratégicos:

Nuclear: Es su origen y mayor fortaleza. Diseñan y construyen reactores nucleares de investigación y producción de radioisótopos (para uso medicinal). Han exportado esta tecnología a países como Egipto, Argelia, Perú, Australia (el reactor OPAL) y recientemente a Países Bajos (el reactor PALLAS).
Espacial: Son los responsables de la ingeniería, integración y construcción de los satélites de ARSAT (telecomunicaciones) y de la serie SAOCOM (observación de la Tierra con radar de microondas). Defensa y Seguridad: Desarrollan radares primarios y secundarios para el control del tráfico aéreo civil y la vigilancia de fronteras (Serie RPA). También trabajan en sistemas de defensa y drones (VANT). Sistemas Médicos: Fabrican equipamiento para radioterapia y medicina nuclear (centros de terapia radiante).


## ARSAT

ARSAT opera uno de los centros de datos más importantes de Sudamérica, ubicado en su Estación Terrena en **Benavídez, Provincia de Buenos Aires**. Cuenta con certificación Tier III, lo que garantiza una disponibilidad del 99.98% del tiempo. Funciona como una "Nube Pública Nacional" donde se alojan datos críticos del Estado Nacional (como registros de identidad, salud y seguridad social) y de empresas privadas que requieren servicios de colocation y hosting de alta seguridad. Gran parte de los datos sensibles de los ciudadanos argentinos (ANSES, AFIP, PAMI, trámites de Mi Argentina).
ARSAT funciona como un nodo de distribución de contenidos (**CDN**). Muchos contenidos educativos o estatales (como la plataforma Educ.ar o el portal de la Televisión Digital) se sirven desde allí, lo que significa que el dato no sale del país para llegar a tu casa.

A través de la red REFEFO de unos 3.600 km de extensión en todo el país conecta a más de **1.100 localidades** y brinda la posibilidad de conexión a unos **18 a 22 millones de personas**.

ARSAT no suele vender internet directamente al vecino, sino que actúa como un **proveedor mayorista**. La combinación del Data Center y la Fibra Óptica tiene un impacto directo en la soberanía y la eficiencia de internet en Argentina:
* Soberanía de Datos
* Reducción de Latencia
* Federalización del Acceso: El tendido de fibra óptica busca romper la centralización geográfica (que antes se limitaba casi exclusivamente al AMBA). Al llevar la fibra a zonas rurales o remotas, se democratiza el acceso a la información y se permite que empresas de cualquier provincia puedan operar con estándares de conectividad internacionales.

**Capacidad de observancia**
ARSAT tiene la capacidad técnica de ver la **metainformación**: origen, destino, volumen de tráfico y protocolos utilizados. No es "ciego" en términos de tráfico masivo. Los IXP (Puntos de Intercambio de Internet) en Argentina están gestionados mayoritariamente por la CABASE (Cámara Argentina de Internet). ARSAT está conectado a ellos, pero el tráfico internacional sale principalmente por los cables submarinos en Las Toninas (operados por empresas como Lumen, Telxius o Google). ARSAT "entrega" el tráfico en esos nodos, y ahí pierde el control directo de la ruta física internacional.

**Cortafuegos**
Great Firewall_ funciona mediante inspección profunda de paquetes (DPI), envenenamiento de DNS y bloqueo de IPs a nivel de frontera nacional. Todo el tráfico que entra o sale de China pasa por una infraestructura estatal que decide qué vive y qué muere.
Gran parte de nuestra vida digital ocurre en servidores de EE. UU. (Google, Amazon, Meta). En ese sentido, la **soberanía de datos** del ciudadano común en Argentina es limitada, ya que aunque el "caño" sea argentino (REFEFO), el "destino" y la "jurisdicción" de los datos siguen siendo extranjeros.


## TRASNFORMACION NECESARIA

**Como debería ser?** ARSAT tendría que dejar de ser un **facilitador de conectividad** para convertirse en un **auditor de flujos**, centralizando no solo los datos estatales en su datacenter, sino la "conciencia" de toda la red nacional.

 En caso que se disponga una ley de inspeccion proofunda de paquetes DPI al estilo chino, ARSAT debería ser dueña de los IXP y los servidores de cada uno de estos IXP verdad?. Es decir, podría tener cacheado informacion de servicio multimedia como youtube o netflix pero allí centralizaría de todo el páis el trafico no solo a servicio multimedia sino la navegacion hacia cualquier otra página. Que quiero decir, que cualquier empresa local, de hecho, deben ser varias, que tienen sus servicios alojados en el extranjero, bueno, esta solicitud llega al IXP y es "verificada", "controlada" y "aprobada" por cada punto de control IXP en lás más de 1.100 localidades alcanzadas

### La estatización o intervención de los IXP

Actualmente, los IXP en Argentina son neutrales y están distribuidos federalmente (la mayoría bajo el ala de CABASE). Los IXP son como "plazas públicas" donde los ISP (Internet Service Providers) intercambian tráfico para que un mail de un cliente de Personal llegue a uno de Movistar sin salir del país. ARSAT debería ser el administrador único de los servidores y _switches_ de esos IXP. Cada nodo en las 1.100 localidades dejaría de ser un punto de intercambio para convertirse en un **punto de inspección**.

### Implementación de $DPI$ (Deep Packet Inspection)

A diferencia del filtrado común (que solo mira la "etiqueta" del paquete: IP de origen y destino), el $DPI$ abre el "paquete" para ver qué hay dentro. Aunque el tráfico esté distribuido, la política de control debe ser central. ARSAT debería instalar _appliances_ de $DPI$ (servidores de altísimo rendimiento) en cada punto de salida. Como bien mencionaste, para que internet no se vuelva extremadamente lento por la inspección, se usarían servidores de **Content Delivery Network (CDN)** locales. Se "cachea" Netflix o YouTube en Benavídez para que el tráfico no tenga que pasar por la "aduana digital" internacional constantemente.


### Flujo de ejemplo de control de las solicitudes al extranjero

Si una empresa local tiene su web en un servidor de Amazon ($AWS$) en EE. UU., el flujo bajo este hipotético control sería:

1. **Solicitud:** El usuario pide entrar a esa web.
2. **Punto de Control (IXP/ARSAT):** El sistema detecta que el destino es una IP extranjera.
3. **Inspección:** El $DPI$ verifica el contenido. Si el sistema encuentra palabras clave prohibidas o protocolos no autorizados (como ciertas VPN), el paquete es "dropeado" (descartado).
4. **Aprobación:** Si pasa los filtros, ARSAT lo entrega a los cables submarinos de las empresas privadas.

### ¿Quién controla la "salida" final?

Para que esto sea 100% efectivo, el Estado debería tener lo que se llama un **"Kill Switch"** o interruptor general en Las Toninas que es donde ingresa el grueso de la fibra que viene de ultramar.

- Incluso si ARSAT controla los IXP, si las empresas privadas (Google, Lumen) permiten que el tráfico pase por sus cables sin filtrar, el control estatal se debilita. Por lo tanto, la ley debería obligar a que **todo cable submarino** tenga como paso previo obligatorio un nodo de inspección de ARSAT.

>[!nota] Nota
>Un **Kill Switch** (o "botón de pánico") es la capacidad técnica y legal para **desconectar o filtrar por completo** el flujo de datos entre el país y el resto del mundo.

**Implementación**

**ntervención en el punto de amarre:** Colocar equipos de conmutación de ARSAT _antes_ de que la fibra del cable submarino se conecte a la red nacional. **El Edificio "Aduana":** Toda la información que llega del mar entraría primero a un edificio controlado por el Estado. Allí, mediante software, se podría "apagar" la conexión con un comando (el Kill Switch lógico) o redirigir todo el tráfico a través de servidores de inspección. el Kill Switch se convierte en un **embudo de inspección**.

En lugar de que cada ISP (como la cooperativa de un pueblo o una gran empresa) tenga su propia salida "libre" hacia los cables submarinos, la ley los obligaría a pasar primero por un **Nodo Centralizado de Tráfico Internacional** gestionado por ARSAT. Muchos proveedores tienen salidas alternativas o cruces de fibra terrestres hacia Chile o Brasil. Para un Kill Switch efectivo, ARSAT debería controlar no solo Las Toninas, sino también cada cruce fronterizo por donde pase un pelo de fibra óptica.
La infraestructura de los 28 **IXP (Internet Exchange Points)** actuales ya funciona como los "peajes" donde se encuentran las redes. Hoy un IXP es una cooperativa o un consorcio privado que usa protocolos de confianza. Para centralizarlos, ARSAT debería reemplazar el equipamiento de red (switches y routers) por hardware propio que soporte **DPI (Deep Packet Inspection)** a escala masiva. En este escenario, el IXP deja de ser solo un punto de intercambio de tráfico para convertirse en un **punto de inspección y auditoría**. Todo lo que se intercambia entre un ISP local y el resto del país pasaría por el "filtro" de ARSAT.
 
> **El proceso sería:**
> 1. El paquete sale de tu casa.
> 2. Viaja por la red local hacia el nodo de ARSAT más cercano.
> 3. Es enviado a la "Gran Aduana Digital" (probablemente en Benavídez o Las Toninas - estan a una distancia por la costa argentina de 350 km)
> 4. Se le aplica **DPI** (Inspección Profunda).>     
> 5. Si es aprobado, se le permite "saltar" al cable submarino hacia el extranjero.


**Fase 1: El Nodo Crítico (Las Toninas)** Es el punto por donde pasa más del **90% del tráfico internacional**. Centralizar y colocar equipos de inspección aquí daría el control inmediato sobre casi todo lo que entra y sale de Argentina hacia el mundo.

**Fase 2: Cruces Terrestres de Alta Capacidad** Controlar las conexiones de fibra que cruzan a Chile (hacia los datacenters de Google allí) y hacia Brasil. Estas son las "vías de escape" si Las Toninas se bloquean.

**Fase 3: Capilaridad Regional** Avanzar sobre los IXP de las capitales provinciales y, finalmente, hacia las redes de las 1.100 localidades.

**Desafios políticos**
* Para que este plan funcione, el Estado debería tener un **catastro físico exacto** de cada pelo de fibra óptica que cruza la frontera. Hoy en día, existen conexiones privadas (cross-border) entre empresas que no pasan necesariamente por nodos públicos.
* Técnicamente es posible, pero requiere una inversión colosal en hardware de procesamiento y una modificación profunda del marco legal de telecomunicaciones.

**Desafíos técnicos de este modelo**

- **El cifrado ($TLS/SSL$):** Hoy casi todo el tráfico web es `https`. El $DPI$ no puede ver el contenido fácilmente a menos que obligue a los usuarios a instalar "certificados raíz" del Estado en sus dispositivos (un método de ataque _Man-in-the-Middle_ institucionalizado).
- **Latencia:** Analizar cada paquete de un país entero en milisegundos requiere una potencia de cómputo colosal que hoy ARSAT no posee.
- **Costo:** Mantener una infraestructura que no solo transporte, sino que "lea" el tráfico de 41 millones de usuarios, multiplicaría por diez el presupuesto operativo.


### El Universo de Usuarios (2026)

- **Población total:** ~46 millones de habitantes.
- **Usuarios de internet:** **41,6 millones** (una penetración del 90,6%).    
- **Conexiones móviles:** 66,6 millones (muchos usuarios tienen más de una línea o dispositivos IoT).

**Paso por la REFEFO (La Red):** Se estima que entre el **40% y el 50% de las localidades del país** dependen de la Red Federal de Fibra Óptica para su conectividad troncal. **Paso por el Data Center:** Menos del **5% del tráfico recreativo** (YouTube, Netflix, etc.) pasa por el Data Center de Benavídez. Sin embargo, casi el **100% de las interacciones con el Estado** (Mi Argentina, AFIP, portales educativos) sí se centralizan allí.

## Cuanta información estamos generando los humanos y cuanto aprendemos de ella y no de repeticiones variadas de algoritmos?

El tráfico generado por **humanos** representa aproximadamente el **68%** del total de las solicitudes $HTTP$ globales. El **32%** restante es tráfico automatizado (bots), y dentro de este grupo, los **AI Crawlers** (los bots que entrenan o alimentan a modelos como GPT-5 o Claude 4) son la categoría de más rápido crecimiento.

Alrededor del **70-75%** de las búsquedas de escritorio se realizan en buscadores tradicionales. Sin embargo, su uso está cayendo (se estima una baja del **25%** para fines de 2026) a medida que los usuarios prefieren respuestas directas. Casi el **60%** de las búsquedas actuales son "zero-click". Esto significa que el usuario obtiene la respuesta directamente de una IA (como las _AI Overviews_ de Google) sin necesidad de entrar a una página web. La interacción directa con modelos de lenguaje representa cerca del **3.2% al 5%** del tráfico de búsqueda total.

Una consulta a un LLM consume hasta **30 veces más energía** que una búsqueda tradicional en Google.

## ARSAT Y LA IA

Para correr una IA como GPT-4 o Llama-3 en servidores propios, se necesitan **clústeres de GPUs** (como las NVIDIA H100 o A100) o **TPUs**. su fuerte no es el cómputo de alta performance ($HPC$) para entrenamiento de IA. Los racks de servidores de IA consumen hasta 10 veces más energía y generan muchísimo más calor que un servidor estándar. Para hospedar modelos "pesados", ARSAT debería actualizar sus sistemas de **refrigeración** (posiblemente a refrigeración líquida) y su densidad eléctrica.

### ¿Qué falta para el reemplazo total?

Para que ARSAT reemplace el uso de modelos extranjeros en el sector público, faltan tres pilares:
- **Hardware Dedicado:** Adquisición masiva de aceleradores de IA (GPUs).
- **Talento Técnico:** No solo ingenieros de red, sino especialistas en _MLOps_ para mantener los modelos corriendo 24/7.
- **Gobernanza:** Reglas claras que obliguen a los organismos públicos a usar la "IA Nacional" en lugar de soluciones fáciles como ChatGPT.

### ¿Cómo se podría comenzar ese reemplazo?
- **Inferencia Local:** Hospedar modelos ya entrenados (como _Llama_ de Meta o _Mistral_) que sean de código abierto. Esto es factible a corto plazo: ARSAT podría poner los "fierros" para que el modelo corra en Benavídez. Los datos no saldrían del país, cumpliendo la ley de soberanía.
- **Entrenamiento Soberano:** Entrenar un modelo desde cero con datos argentinos (modismos, leyes, historia local). Esto requiere una inversión en hardware que hoy Argentina suele tercerizar o realizar en colaboración con universidades como la UBA o el Conicet, que tienen supercomputadoras (como la _Tupac_ o la _Clementina XXI_).


Tanto **Tupac** como **Clementina XXI** son supercomputadoras basadas en la arquitectura tradicional de silicio (como la de cualquier servidor, pero a una escala masiva de interconexión). Son el verdadero motor para entrenar un Modelo de Lenguaje ($LLM$) soberano.

#### Clementina XXI (La Joya del Cómputo Nacional)

Instalada en el Centro de Cómputos del Servicio Meteorológico Nacional (SMN) e inaugurada recientemente bajo el ala del Ministerio de Ciencia y Tecnología y el CONICET, es **una de las supercomputadoras más potentes de América Latina**.

- **Para qué sirve en IA:** A diferencia de las supercomputadoras más viejas que solo usaban procesadores tradicionales ($CPU$), Clementina XXI fue diseñada con **aceleradores gráficos (GPUs NVIDIA)** de alta gama. Esto la convierte en la candidata ideal para el entrenamiento y ajuste fino (_fine-tuning_) de modelos de IA con millones de parámetros, utilizando textos históricos, leyes argentinas, digitalizaciones de la UBA y registros culturales.

#### Tupac (El antecedente histórico)

Ubicada en el Centro de Simulación Computacional (CSC) del CONICET.

- **Su rol:** Aunque es un equipo más veterano enfocado originalmente en simulación física, diseño aeronáutico y modelado de fluidos, demostró la capacidad del sistema científico argentino para gestionar clusters distribuidos. Hoy queda un paso atrás de Clementina XXI para tareas de IA profunda debido a la arquitectura de sus nodos, pero sigue siendo un pilar de aprendizaje en ingeniería de datos.


**LIAA (Laboratorio de Inteligencia Artificial Aplicada - UBA):** Ubicado en el Pabellón "0+Infinito" de Ciudad Universitaria, este centro (dependiente del Departamento de Computación de la Facultad de Ciencias Exactas y Naturales) es el líder indiscutido. Científicos como Diego Fernández Slezak y su equipo investigan la estructura interna de los Grandes Modelos de Lenguaje ($LLM$). 

**Instituto de Cálculo "Rebeca Cherep de Guber" (CONICET - UBA):** El Co-Laboratorio de Ciencia de Datos e IA, liderado por investigadoras como Laura Ación, está especializado en el **uso responsable y ético de datos**. Su aporte es crucial para auditar que el modelo nacional no reproduzca sesgos discriminatorios y que la privacidad de los datos de entrenamiento esté blindada.

**Pladema (Universidad Nacional del Centro - UNICEN, Tandil):** Un histórico polo tecnológico que, en conjunto con el CONICET, desarrolla optimización de algoritmos y computación gráfica de alto rendimiento. Tienen una larga trayectoria transfiriendo desarrollos de software complejo hacia el sector público y privado.


### La Orquestación de Cómputo e Ingeniería de Datos (MLOps)

Para coordinar que los algoritmos de la UBA corran sobre los fierros de la supercomputadora Clementina XXI y terminen empaquetados en el Data Center de ARSAT, se necesitan ingenieros de sistemas a gran escala.

**CSC (Centro de Simulación Computacional - CONICET):** Ubicado en el Polo Científico Tecnológico, este centro domina la Computación de Alto Desempeño ($HPC$). Aunque históricamente modelaban física o fluidos, sus ingenieros tienen el _know-how_ técnico para exprimir la capacidad de supercomputadores como _Tupac_ o la infraestructura del Servicio Meteorológico.

**CID (Centro Interinstitucional de Ciencia de Datos):** Articula proyectos de datos de alto impacto entre múltiples disciplinas y universidades, funcionando como el puente perfecto para la transferencia tecnológica hacia organismos estatales.


### ¿Con qué "soberanía de hardware" contamos?

A nivel de fabricación de silicio o hardware de red complejo, **Argentina no produce equipamiento propio**; depende 100% de la importación de estos componentes. Si el proyecto de centralización y control que veníamos trazando se pusiera en marcha, el país se enfrentaría a un dilema geopolítico:

- Para la **capa algorítmica e intelectual** (la materia gris), tenemos la UBA, el CONICET e INVAP.    
- Pero para los **"fierros"** que ejecutan esa lógica (los switches y cortafuegos masivos), Argentina tendría que decidir si financiar su infraestructura comprándole tecnología de inspección a **China** (que ya tiene los equipos con funciones de filtrado masivo integradas de fábrica) o seguir utilizando la arquitectura tradicional **estadounidense**, asumiendo que los sistemas de seguridad occidentales responderían a las directrices del Estado local.

### 1. La Capa de Acceso (OLTs y ONTs)

Esta es la tecnología que "ilumina" la fibra óptica hacia los hogares y empresas (el equipamiento de última milla). Aquí **China tiene un dominio casi absoluto** debido a sus costos competitivos y agresiva financiación durante la última década.

- **Marcas dominantes:** **Huawei** y **ZTE**.
- **Presencia nacional:** La gran mayoría de las cooperativas locales y PyMEs ISP que se conectan a los nodos de ARSAT utilizan OLTs chinas. En el mercado residencial, las ONTs (los módems de fibra que están en las casas de los usuarios) son en un 85-90% de origen chino.
- **Alternativas occidentales:** Existe equipamiento de **Nokia** (origen europeo/finlandés) y **Calix** o **Adtran** (EE. UU.), utilizado principalmente por grandes operadores (como Telecom o Claro) en nodos troncales de acceso, pero en menor volumen que las marcas asiáticas.
    

### 2. El Núcleo de Red y Enrutamiento (Routers de Core y Switches de IXP)

En el "corazón" de internet, donde se decide hacia dónde van los paquetes a nivel mayorista (los nodos centrales de ARSAT y los switches principales de los IXP), la balanza está muy repartida entre **Estados Unidos y China**.

- **Líderes Estadounidenses:** **Cisco Systems** y **Juniper Networks**. Tradicionalmente, la arquitectura de internet en Argentina se construyó sobre Cisco. ARSAT y los IXP más grandes (como el de Buenos Aires) tienen un porcentaje altísimo de routers de core de estas marcas debido a su estabilidad y soporte de protocolos complejos (como BGP o MPLS).
- **El avance de China:** **Huawei** ha ganado un terreno enorme en el transporte mayorista y nodos de agregación intermedios. Gran parte de las ampliaciones de la REFEFO de los últimos años incorporaron tecnología de transporte óptico (DWDM) de origen chino.
    

### 3. Seguridad y Cortafuegos (Firewalls Perimetrales)

Para la capa de ciberseguridad (los equipos encargados de mitigar ataques y que, en el hipotético escenario que discutíamos, harían la inspección profunda), el mercado corporativo y estatal argentino es **abrumadoramente estadounidense**.

- **Marcas principales:** **Fortinet**, **Palo Alto Networks** y **Check Point** (origen israelí, pero con fuerte base en EE. UU.).
- **Disponibilidad:** El Data Center de ARSAT en Benavídez y los entornos de nube gubernamentales se protegen principalmente con appliances físicos y lógicos de estas marcas. El software y los chips ASIC de estos equipos están diseñados específicamente para procesar reglas de firewall a nivel de hardware, pero su propiedad intelectual y actualizaciones dependen directamente de casas matrices en el exterior.


|**Tipo de Equipo**|**Función Clave**|**Origen Dominante**|**Marcas Principales en el País**|
|---|---|---|---|
|**Routers de Core / Switches IXP**|Enrutamiento masivo|🇺🇸 EE. UU. / 🇨🇳 China|Cisco, Juniper, Huawei|
|**OLTs / ONTs**|Distribución de Fibra (Última milla)|🇨🇳 China|Huawei, ZTE, Nokia|
|**Cortafuegos / Firewalls**|Seguridad e Inspección|🇺🇸 EE. UU. / 🇮🇱 Israel|Fortinet, Palo Alto, Check Point|
|**Sistemas Ópticos (DWDM)**|Iluminar los miles de km de fibra|🇨🇳 China / 🇫🇷 Europa|Huawei, Nokia (ex Alcatel-Lucent)|

### El Socio Local: ¿Quién tiene la capacidad de producir los "Fierros"?

Aquí es donde debemos separar la **ingeniería de diseño** de la **fabricación en serie**.

#### El rol de INVAP: El Diseñador, no la Fábrica

Como bien suponías, **INVAP** es lo más avanzado que tenemos, pero con un matiz importante: **INVAP es una casa de alta ingeniería de diseño complejo, no una planta de producción masiva de electrónica de consumo**.
- **Su fuerte:** INVAP tiene la capacidad de diseñar placas de circuito integrado de alta complejidad, sistemas embebidos, aviónica para satélites y la lógica de radares.
- **En el Joint Venture:** INVAP sería el socio ideal para tomar el software y la arquitectura china y "argentinizarla", auditando el código fuente para asegurar que no existan puertas traseras (_backdoors_) extranjeras y adaptando los protocolos a las necesidades del Estado y de ARSAT.

#### La verdadera "Fábrica": Pixart (La planta inteligente)

Si buscamos quién tiene las máquinas para soldar componentes y ensamblar placas a nivel industrial 4.0 hoy en el país, la respuesta es **Pixart**.
- **Qué es:** Ubicada en el Parque Industrial de Escobar, es la primera "fábrica inteligente" automatizada y robótica de Latinoamérica.
- **Su capacidad:** Cuenta con alianzas para ensamblar componentes complejos y microchips de avanzada (trabajando en conjunto con firmas globales). Tienen líneas de producción automatizadas operadas por brazos robóticos capaces de fabricar hardware a gran escala.
- **En el Joint Venture:** Si ARSAT e INVAP diseñan el Cortafuegos Nacional junto al socio chino, **Pixart** es la que tiene la capacidad física real en el territorio para fabricar las placas madre, soldar los procesadores y ensamblar los _racks_ perimetrales que luego se instalarían en los IXP.

### El esquema ideal para el Joint Venture

Para revivir el espíritu de FATE pero con la tecnología actual, el consorcio de soberanía en hardware debería estructurarse de la siguiente manera:

1. **Socio Tecnológico (Huawei / Sangfor):** Transfiere la tecnología de los chips de red y el sistema operativo base del firewall.
2. **INVAP:** Modifica el diseño, audita la ciberseguridad del firmware y asegura que el control lógico de las llaves de cifrado quede en manos argentinas.
3. **Pixart (o Mirgor):** Actúa como la planta de fabricación local, imprimiendo las placas y ensamblando el hardware físico bajo normas de calidad industrial.
4. **ARSAT:** El cliente ancla que compra estos equipos para nacionalizar los IXP y la REFEFO.

### Ranking de ISPs en linea fija Argentina (Por Volumen de Clientes)

El mercado de internet fijo en Argentina cuenta con aproximadamente **11.5 millones de accesos**. La distribución de cuota de mercado se concentra de la siguiente manera:

# Ranking ISP — Internet fija en Argentina

Accesos de banda ancha fija (residencial + empresarial) · Datos a 2024–2025

Total accesos fijos ~12,25 M
Penetración hogares 82%
Operadores activos 700+

| Operador                                                              | Accesos est. | Participación |
| --------------------------------------------------------------------- | ------------ | ------------- |
| Telecom ArgentinaLíderFibertel / Personal Flow / Cablevisión          | ~5.63 M      | 46%           |
| Movistar (Telefónica / TMA)DSL + fibra FTTH · en transición a Telecom | ~1.84 M      | 15%           |
| TelecentroCABA y Gran Buenos Aires (HFC + fibra)                      | ~1.35 M      | 11%           |
| Claro (AMX Argentina)Fibra óptica FTTH a nivel nacional               | ~1.10 M      | 9%            |
| SupercanalMendoza, San Juan, Tucumán, Córdoba                         | ~0.49 M      | 4%            |
| iPlanFTTH puro · AMBA, Córdoba, Rosario                               | ~0.37 M      | 3%            |
| DirecTV / StarlinkSatélite · principalmente zonas rurales             | ~0.24 M      | 2%            |
| Cooperativas y pymes~700 operadores en el interior del país           | ~1.23 M      | 10%           |

### Grupo Clarin - La Fusión Histórica (Telecom + Cablevisión)

Hasta el año 2017, el Grupo Clarín era el dueño mayoritario de **Cablevisión** (televisión por cable y banda ancha a través de Fibertel), mientras que **Telecom Argentina** (telefonía fija, móvil con Personal y banda ancha con Arnet) pertenecía en su estructura de control al fondo de inversión **Fintech**, liderado por el financiero mexicano David Martínez.

En **junio de 2017**, ambas compañías anunciaron una fusión por absorción que fue aprobada definitivamente por los organismos reguladores del Estado en 2018. Cablevisión fue absorbida por Telecom Argentina, dando origen al mayor conglomerado de telecomunicaciones del país, con capacidad de ofrecer "cuádruple play" (telefonía fija, móvil, internet y televisión bajo una misma infraestructura).

### 2. El Vehículo Societario: Cablevisión Holding (CVH)

Para concretar la operación sin mezclar directamente el negocio de los medios tradicionales (diarios, radios, canales de aire) con el de las telecomunicaciones, el Grupo Clarín realizó previamente una **escisión** (separación de activos).

- **Grupo Clarín S.A.:** Se quedó exclusivamente con los medios de comunicación e industrias gráficas (Diario Clarín, Olé, Canal 13, TN, Radio Mitre, Papel Prensa, etc.).
- **Cablevisión Holding S.A. (CVH):** Se creó como una nueva empresa cotizante dedicada únicamente al negocio del desarrollo de infraestructura de telecomunicaciones.
    
Los **accionistas mayoritarios e históricos del Grupo Clarín** (las familias Magnetto, Noble Herrera, Aranda y Pagliaro) pasaron a controlar de forma directa el paquete mayoritario de esta nueva sociedad corporativa (CVH).

### 3. Composición Accionaria de Telecom Argentina

Como resultado de esa fusión, la estructura de propiedad y control político de Telecom Argentina quedó distribuida esencialmente en tres grandes bloques de socios:

```
                  ┌────────────────────────────────────────┐
                  │       TELECOM ARGENTINA S.A.           │
                  └───────────────────┬────────────────────┘
                                      │
       ┌──────────────────────────────┼──────────────────────────────┐
       ▼                              ▼                              ▼
[ Cablevisión Holding ]         [ Fintech Media ]            [ Capital Flotante / FGS ]
  Aprox. 39% - 40%               Aprox. 31% - 32%                Aprox. 28% - 29%
(Familias controlantes          (David Martínez)             (Bolsas de BA y NY / ANSES)
   del Grupo Clarín)
```

- **Cablevisión Holding (CVH):** Posee aproximadamente el **39% - 40%** de las acciones directas de Telecom Argentina. Al ser los mismos beneficiarios finales del multimedio, representan el ala operativa y de conducción de Clarín dentro de la telefónica.
- **Fintech Advisory (David Martínez):** Mantiene cerca del **31% - 32%** de las acciones. Ambas partes (CVH y Fintech) firmaron un acuerdo de accionistas para ejercer el control conjunto de la compañía, unificando sus votos en las decisiones estratégicas de la empresa.
- **Capital Flotante y Estado (Anses):** El porcentaje restante (cercano al **28% - 29%**) cotiza públicamente en las bolsas de Buenos Aires y Nueva York ($TEO$), e incluye la participación histórica que mantiene el Estado argentino a través del Fondo de Garantía de Sustentabilidad de la ANSES.
    

> **En resumen:** Aunque jurídica y contablemente el Grupo Clarín (medios) y Telecom Argentina (telecomunicaciones) operan como dos empresas con personerías e ingresos separados, **comparten a los mismos dueños y directivos en la cima de su pirámide societaria.**

## Identidad

Xi Jinping destaca que la historia china posee millones de años de historia humana y cinco mil años de civilización. Argumenta que esta continuidad histórica hace que el modelo chino de desarrollo sea único, distinto al occidental y adaptado a sus propias realidades
**La cultura como motor de modernización:** Sostiene que la herencia cultural no es solo un registro del pasado, sino una herramienta fundamental para guiar el futuro, promover la unidad nacional y aumentar el "poder blando" de China en la arena internacional. 56 etnias reconocidas oficialmente.

En América Latina existen ==**más de 800 pueblos indígenas** distintos==, que en conjunto suman cerca de 58 millones de personas. A nivel macro, la diversidad cultural y étnica se compone de una fusión de grupos principales: **amerindios, afrodescendientes, mestizos, descendientes de europeos y comunidades asiáticas y árabes**. La región alberga a decenas de millones de indígenas, siendo Brasil el país con mayor número de pueblos originarios (más de 240), y países como México y Perú los que concentran las poblaciones nativas más numerosas en términos absolutos y Perú los que concentran las poblaciones nativas más numerosas en términos absolutos.


---
#### Referencias

