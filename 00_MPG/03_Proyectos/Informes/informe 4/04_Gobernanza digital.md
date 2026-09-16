tipo::
tags::
fecha:: 2026-09-11
links::
links_externos::


La gobernanza del espacio digital en Argentina hace referencia a la creación y aplicación de reglas, normas y políticas públicas para gestionar internet, los datos y la tecnología. Esto involucra al Estado, a las empresas privadas y a los ciudadanos. Pilares ClaveMarcos normativos: Leyes y regulaciones sobre el uso de la red, la ciberseguridad y la protección de datos personales.Soberanía digital: El control de la infraestructura crítica, como centros de datos y redes de conectividad en el país.Gobierno abierto: El uso de herramientas digitales por parte del Estado para dar mejor servicio, trámites ágiles y transparencia a las personas.Participación plural: Espacios de debate donde la sociedad civil y la comunidad técnica opinan sobre el rumbo de internet.


En Argentina están gestionados mayoritariamente por la CABASE (Cámara Argentina de Internet) pero son propiedad de, en general, empresas privadas que responden a las mismas que monopolizan la oferta del servicio de internet (Telecom, Telefónica, Telecentro como los principales).

ARSAT debería ser el administrador único de los servidores y _switches_ de esos IXP. Cada nodo en las 1.100 localidades dejaría de ser un punto de intercambio para convertirse en un **punto de inspección**.

ARSAT está conectado a estos “centros distribuidores” pero el tráfico principal no surca por los cables internos sino que son consultas destinadas a servidores en el extranjero por lo tanto desde estos IXP se dirigen principalmente por los cables submarinos en Las Toninas (operados por empresas como Lumen, Telxius o Google). ARSAT "entrega" el tráfico en esos nodos, y ahí pierde el control directo de la ruta física internacional.

**Por lo tanto, cuanto controla ARSAT del tráfico de internet y que capacidad tenemos de, ante un ataque cortar el internet, bloquear accesos masivamente o evitar robo de datos?**

ARSAT hoy funciona como un **facilitador de conectividad** no audita los paquetes de datos que viajan por sus cables. Por allí circula todo el tiempo la atención de "la conciencia" de la red nacional. Familias enteras mirando algún canal de streaming, mirando una película, cualquier persona desde su celular leyendo un portal o navegando por una red social. El contenido en general tiene por destino las fuentes de este contenido que se almacenan en servidores del extranjero.

Así como “salen” conexiones a cualquier servidor de cualquier país sin restricción, también “entran” conexiones de cualquier tipo. En Venezuela, esto significó un ataque masivo de denegación de servicios a la página que estaba siendo utilizada para centralizar las elecciones en junio de 2025. Nosotros disponemos de infraestructura que tienen un grado de desarrollo donde por poco más podríamos “cercar” la entrada a nuestro “territorio digital” para evitar este tipo de ataques. En parte es que ARSAT desarrolle el software y las capacidades técnicas de trabajadores que permita la inspeccion proofunda de paquetes  de datos (DPI) al estilo chino como hace con “El Gran Cortafuegos Chino”. ARSAT debería ser dueña o tener control total de los servidores de los IXP. Es decir, podría tener guardado información de servicio multimedia como youtube o netflix para no viajar constantemente hacia EE.UU por contenido repetido, y también desde allí centralizaría de todo el páis el trafico no solo a servicio multimedia sino la navegacion hacia cualquier otra página. Que quiero decir, que cualquier empresa local, que tienen sus servicios alojados en el extranjero, bueno, esta solicitud llega al IXP y es "verificada", "controlada" y "aprobada" por cada punto de control IXP.

**El Gran Cortafuegos Chino, es un paso más allá ya que esta en la entrada del internet que comunica a China hacia afuera. Es un muro donde, la seguridad la define el partido chino.** ARSAT podría funcionar de la misma manera.

Colocar equipos de conmutación de ARSAT _antes_ de que la fibra del cable submarino se conecte a la red nacional. **El Edificio "Aduana":** Toda la información que llega del mar entraría primero a un edificio controlado por el Estado. Allí, mediante software, se podría "apagar" la conexión con un comando (el Kill Switch lógico) o redirigir todo el tráfico a través de servidores de inspección. el Kill Switch se convierte en un **embudo de inspección**.

En lugar de que cada ISP (como la cooperativa de un pueblo o una gran empresa) tenga su propia salida "libre" hacia los cables submarinos, la ley los obligaría a pasar primero por un **Nodo Centralizado de Tráfico Internacional** gestionado por ARSAT. Muchos proveedores tienen salidas alternativas o cruces de fibra terrestres hacia Chile o Brasil. Para un Kill Switch efectivo, ARSAT debería controlar no solo Las Toninas, que es el punto por donde pasa más del **90% del tráfico internacional** sino también cada cruce fronterizo por donde pase un pelo de fibra óptica. Estas **Cruces Terrestres de Alta Capacidad** deberían Controlar las conexiones de fibra que cruzan a Chile (hacia los datacenters de Google allí) y hacia Brasil. Para que este plan funcione, deberíamos tener un catastro físico exacto de cada pelo de fibra óptica que cruza la frontera. Hoy en día, existen conexiones privadas (cross-border) entre empresas que no pasan necesariamente por nodos públicos.

  
La infraestructura de los 28 **IXP (Internet Exchange Points)** actuales ya funciona como los "peajes" donde se encuentran las redes. Hoy un IXP es un consorcio privado donde priman los intereses de quienes concentran el servicio de proveduría de internet del país, quienes definen los protocolos de confianza y la “entrada” y “salida” de los datos. Para centralizarlos, ARSAT debería reemplazar el equipamiento de red (switches y routers) por hardware propio que soporte **DPI (Deep Packet Inspection)** a escala masiva. En este escenario, el IXP deja de ser solo un punto de intercambio de tráfico para convertirse en un **punto de inspección y auditoría**. Todo lo que se intercambia entre un ISP local y el resto del país pasaría por el "filtro" de ARSAT.

**El proceso sería:**

1.      El paquete sale de tu casa.

2.      Viaja por la red local hacia el nodo de ARSAT más cercano.

3.      Es enviado a la "Gran Aduana Digital" (probablemente en Benavídez o Las Toninas - estan a una distancia por la costa argentina de 350 km)

4.      Se le aplica **DPI** (Inspección Profunda)

5.      Si es aprobado, se le permite "saltar" al cable submarino hacia el extranjero.

- **El cifrado (TLS/SSL):** Hoy casi todo el tráfico web es https. El DPI no puede ver el contenido fácilmente a menos que obligue a los usuarios a instalar "certificados raíz" del Estado en sus dispositivos (un método de ataque _Man-in-the-Middle_ institucionalizado).
- **Latencia:** Analizar cada paquete de un país entero en milisegundos requiere una potencia de cómputo colosal que hoy ARSAT no posee.
- **Costo:** Mantener una infraestructura que no solo transporte, sino que "lea" el tráfico de 41 millones de usuarios, multiplicaría por diez el presupuesto operativo.

A diferencia del filtrado común (que solo mira la "etiqueta" del paquete: IP de origen y destino), el DPI abre el "paquete" para ver qué hay dentro. ARSAT debería instalar _appliances_ de DPI (servidores de altísimo rendimiento) en cada punto de salida. Como bien mencionaste, para que internet no se vuelva extremadamente lento por la inspección, se usarían servidores de **Content Delivery Network (CDN)** locales. Se "cachea" Netflix o YouTube en Benavídez para que el tráfico no tenga que pasar por la "aduana digital" internacional constantemente.

**Flujo de ejemplo de control de las solicitudes al extranjero**

Si una empresa local tiene su web en un servidor de Amazon (AWS) en EE. UU., el flujo bajo este hipotético control sería:

1. **Solicitud:** El usuario pide entrar a esa web.
2. **Punto de Control (IXP/ARSAT):** El sistema detecta que el destino es una IP extranjera.
3. **Inspección:** El DPI verifica el contenido. Si el sistema encuentra palabras clave prohibidas o protocolos no autorizados (como ciertas VPN), el paquete es "dropeado" (descartado).
4. **Aprobación:** Si pasa los filtros, ARSAT lo entrega a los cables submarinos de las empresas privadas.

Para que ARSAT reemplace el uso de modelos extranjeros en el sector público, faltan tres pilares:

- **Hardware Dedicado:** Adquisición masiva de aceleradores de IA (GPUs).
- **Talento Técnico:** No solo ingenieros de red, sino especialistas en _MLOps_ para mantener los modelos corriendo 24/7.
- **Gobernanza:** Reglas claras que obliguen a los organismos públicos a usar la "IA Nacional" en lugar de soluciones fáciles como ChatGPT.


#### INFRA

**Ranking ISP — Internet fija en Argentina**

Accesos de banda ancha fija (residencial + empresarial) · Datos a 2024–2025

Total accesos fijos ~12,25 M  
Penetración hogares 82%  
Operadores activos 700+

|**Operador**|**Accesos est.**|**Participación**|
|---|---|---|
|Telecom ArgentinaLíderFibertel / Personal Flow / Cablevisión|~5.63 M|46%|
|Movistar (Telefónica / TMA)DSL + fibra FTTH · en transición a Telecom|~1.84 M|15%|
|TelecentroCABA y Gran Buenos Aires (HFC + fibra)|~1.35 M|11%|
|Claro (AMX Argentina)Fibra óptica FTTH a nivel nacional|~1.10 M|9%|
|SupercanalMendoza, San Juan, Tucumán, Córdoba|~0.49 M|4%|
|iPlanFTTH puro · AMBA, Córdoba, Rosario|~0.37 M|3%|
|DirecTV / StarlinkSatélite · principalmente zonas rurales|~0.24 M|2%|
|Cooperativas y pymes~700 operadores en el interior del país|~1.23 M|10%|

**Grupo Clarin - La Fusión Histórica (Telecom + Cablevisión)**

Hasta el año 2017, el Grupo Clarín era el dueño mayoritario de **Cablevisión** (televisión por cable y banda ancha a través de Fibertel), mientras que **Telecom Argentina** (telefonía fija, móvil con Personal y banda ancha con Arnet) pertenecía en su estructura de control al fondo de inversión **Fintech**, liderado por el financiero mexicano David Martínez.

En **junio de 2017**, ambas compañías anunciaron una fusión por absorción que fue aprobada definitivamente por los organismos reguladores del Estado en 2018. Cablevisión fue absorbida por Telecom Argentina, dando origen al mayor conglomerado de telecomunicaciones del país, con capacidad de ofrecer "cuádruple play" (telefonía fija, móvil, internet y televisión bajo una misma infraestructura).

**2. El Vehículo Societario: Cablevisión Holding (CVH)**

Para concretar la operación sin mezclar directamente el negocio de los medios tradicionales (diarios, radios, canales de aire) con el de las telecomunicaciones, el Grupo Clarín realizó previamente una **escisión** (separación de activos).

- **Grupo Clarín S.A.:** Se quedó exclusivamente con los medios de comunicación e industrias gráficas (Diario Clarín, Olé, Canal 13, TN, Radio Mitre, Papel Prensa, etc.).
- **Cablevisión Holding S.A. (CVH):** Se creó como una nueva empresa cotizante dedicada únicamente al negocio del desarrollo de infraestructura de telecomunicaciones.

Los **accionistas mayoritarios e históricos del Grupo Clarín** (las familias Magnetto, Noble Herrera, Aranda y Pagliaro) pasaron a controlar de forma directa el paquete mayoritario de esta nueva sociedad corporativa (CVH).

**3. Composición Accionaria de Telecom Argentina**

Como resultado de esa fusión, la estructura de propiedad y control político de Telecom Argentina quedó distribuida esencialmente en tres grandes bloques de socios:

                  ┌────────────────────────────────────────┐

                  │       TELECOM ARGENTINA S.A.           │

                  └───────────────────┬────────────────────┘

                                      │

       ┌──────────────────────────────┼──────────────────────────────┐

       ▼                              ▼                              ▼

[ Cablevisión Holding ]         [ Fintech Media ]            [ Capital Flotante / FGS ]

  Aprox. 39% - 40%               Aprox. 31% - 32%                Aprox. 28% - 29%

(Familias controlantes          (David Martínez)             (Bolsas de BA y NY / ANSES)

   del Grupo Clarín)

- **Cablevisión Holding (CVH):** Posee aproximadamente el **39% - 40%** de las acciones directas de Telecom Argentina. Al ser los mismos beneficiarios finales del multimedio, representan el ala operativa y de conducción de Clarín dentro de la telefónica.
- **Fintech Advisory (David Martínez):** Mantiene cerca del **31% - 32%** de las acciones. Ambas partes (CVH y Fintech) firmaron un acuerdo de accionistas para ejercer el control conjunto de la compañía, unificando sus votos en las decisiones estratégicas de la empresa.
- **Capital Flotante y Estado (Anses):** El porcentaje restante (cercano al **28% - 29%**) cotiza públicamente en las bolsas de Buenos Aires y Nueva York (TEO), e incluye la participación histórica que mantiene el Estado argentino a través del Fondo de Garantía de Sustentabilidad de la ANSES.

**En resumen:** Aunque jurídica y contablemente el Grupo Clarín (medios) y Telecom Argentina (telecomunicaciones) operan como dos empresas con personerías e ingresos separados, **comparten a los mismos dueños y directivos en la cima de su pirámide societaria.**



---
#### Referencias

