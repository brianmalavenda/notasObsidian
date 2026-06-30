tipo::
tags::
fecha:: 2026-06-30
links::
links_externos::


Actualmente, se calcula de que un 90,6% de habitantes del país tienen conexión a internet, es decir, **41,6 millones** de 47. **Conexiones móviles son más,** 66,6 millones (muchos usuarios tienen más de una línea o dispositivos IoT). ARSAT funciona como un nodo de distribución de contenidos (**CDN**). Muchos contenidos educativos o estatales (como la plataforma Educ.ar o el portal de la Televisión Digital) se sirven desde allí, lo que significa que el dato no sale del país para llegar a tu casa.

ARSAT opera uno de los centros de datos más importantes de Sudamérica, ubicado en su Estación Terrena en **Benavídez, Provincia de Buenos Aires**. Cuenta con certificación Tier III, lo que garantiza una disponibilidad del 99.98% del tiempo. Funciona como una "Nube Pública Nacional" donde se alojan datos críticos del Estado Nacional (como registros de identidad, salud y seguridad social) y de empresas privadas que requieren servicios de colocation y hosting de alta seguridad. Gran parte de los datos sensibles de los ciudadanos argentinos (ANSES, AFIP, PAMI, trámites de Mi Argentina).

A través de la red REFEFO de unos 36.000 km de extensión en todo el país conecta a más de **1.100 localidades** y brinda la posibilidad de conexión a unos **18 a 22 millones de personas**. Se estima que entre el **40% y el 50% de las localidades del país** dependen de la Red Federal de Fibra Óptica para su conectividad troncal. Menos del **5% del tráfico recreativo** (YouTube, Netflix, etc.) pasa por el Data Center de Benavídez. Sin embargo, casi el **100% de las interacciones con el Estado** (Mi Argentina, AFIP, portales educativos) sí se centralizan allí.

ARSAT no suele vender internet directamente al hogar de familia, sino que actúa como un **proveedor mayorista**. La combinación del Data Center y la Fibra Óptica tiene un impacto directo en la soberanía y la eficiencia de internet en Argentina: Control de los Datos, Reducción de Latencia(demoras entre una solicitud y una respuesta), Federalización del Acceso: El tendido de fibra óptica busca romper la centralización geográfica (que antes se limitaba casi exclusivamente al AMBA). Al llevar la fibra a zonas rurales o remotas, se democratiza el acceso a la información y se permite que empresas de cualquier provincia puedan operar con estándares de conectividad internacionales.

  
ARSAT tiene la capacidad técnica de ver la **metainformación**: origen, destino, volumen de tráfico y protocolos utilizados. No es "ciego" en términos de tráfico masivo. Los IXP (Puntos de Intercambio de Internet) son como "plazas públicas" donde los ISP (Internet Service Providers) intercambian tráfico para que un mail de un cliente que tiene contratado el internet de Personal llegue a uno que tiene internet de Telefonica sin salir del país. Además funcionan como un punto donde las series o videos que ya vio alguien de por ejemplo Netflix, no se tiene que volver a ir a buscar la información hasta EE.UU. sino que se guarda temporalmente allí.

En Argentina están gestionados mayoritariamente por la CABASE (Cámara Argentina de Internet) pero son propiedad de, en general, empresas privadas que responden a las mismas que monopolizan la oferta del servicio de internet (Telecom, Telefónica, Telecentro como los principales).

ARSAT debería ser el administrador único de los servidores y _switches_ de esos IXP. Cada nodo en las 1.100 localidades dejaría de ser un punto de intercambio para convertirse en un **punto de inspección**.

ARSAT está conectado a estos “centros distribuidores” pero el tráfico principal no surca por los cables internos sino que son consultas destinadas a servidores en el extranjero por lo tanto desde estos IXP se dirigen principalmente por los cables submarinos en Las Toninas (operados por empresas como Lumen, Telxius o Google). ARSAT "entrega" el tráfico en esos nodos, y ahí pierde el control directo de la ruta física internacional.

**Por lo tanto, cuanto controla ARSAT del tráfico de internet y que capacidad tenemos de, ante un ataque cortar el internet, bloquear accesos masivamente o evitar robo de datos?**

ARSAT hoy funciona como un **facilitador de conectividad** no audita los paquetes de datos que viajan por sus cables. Por allí circula todo el tiempo la atención de "la conciencia" de la red nacional. Familias enteras mirando algún canal de streaming, mirando una película, cualquier persona desde su celular leyendo un portal o navegando por una red social. El contenido en general tiene por destino las fuentes de este contenido que se almacenan en servidores del extranjero.

Así como “salen” conexiones a cualquier servidor de cualquier país sin restricción, también “entran” conexiones de cualquier tipo. En Venezuela, esto significó un ataque masivo de denegación de servicios a la página que estaba siendo utilizada para centralizar las elecciones en junio de 2025. Nosotros disponemos de infraestructura que tienen un grado de desarrollo donde por poco más podríamos “cercar” la entrada a nuestro “territorio digital” para evitar este tipo de ataques. En parte es que ARSAT desarrolle el software y las capacidades técnicas de trabajadores que permita la inspeccion proofunda de paquetes  de datos (DPI) al estilo chino como hace con “El Gran Cortafuegos Chino”. ARSAT debería ser dueña o tener control total de los servidores de los IXP. Es decir, podría tener guardado información de servicio multimedia como youtube o netflix para no viajar constantemente hacia EE.UU por contenido repetido, y también desde allí centralizaría de todo el páis el trafico no solo a servicio multimedia sino la navegacion hacia cualquier otra página. Que quiero decir, que cualquier empresa local, que tienen sus servicios alojados en el extranjero, bueno, esta solicitud llega al IXP y es "verificada", "controlada" y "aprobada" por cada punto de control IXP.

**El Gran Cortafuegos Chino, es un paso más allá ya que esta en la entrada del internet que comunica a China hacia afuera. Es un muro donde, la seguridad la define el partido chino.** ARSAT podría funcionar de la misma manera.

Colocar equipos de conmutación de ARSAT _antes_ de que la fibra del cable submarino se conecte a la red nacional. **El Edificio "Aduana":** Toda la información que llega del mar entraría primero a un edificio controlado por el Estado. Allí, mediante software, se podría "apagar" la conexión con un comando (el Kill Switch lógico) o redirigir todo el tráfico a través de servidores de inspección. el Kill Switch se convierte en un **embudo de inspección**.

En lugar de que cada ISP (como la cooperativa de un pueblo o una gran empresa) tenga su propia salida "libre" hacia los cables submarinos, la ley los obligaría a pasar primero por un **Nodo Centralizado de Tráfico Internacional** gestionado por ARSAT. Muchos proveedores tienen salidas alternativas o cruces de fibra terrestres hacia Chile o Brasil. Para un Kill Switch efectivo, ARSAT debería controlar no solo Las Toninas, que es el punto por donde pasa más del **90% del tráfico internacional** sino también cada cruce fronterizo por donde pase un pelo de fibra óptica. Estas **Cruces Terrestres de Alta Capacidad** deberían Controlar las conexiones de fibra que cruzan a Chile (hacia los datacenters de Google allí) y hacia Brasil. Para que este plan funcione, deberíamos tener un catastro físico exacto de cada pelo de fibra óptica que cruza la frontera. Hoy en día, existen conexiones privadas (cross-border) entre empresas que no pasan necesariamente por nodos públicos.

  
La infraestructura de los 28 **IXP (Internet Exchange Points)** actuales ya funciona como los "peajes" donde se encuentran las redes. Hoy un IXP es una cooperativa o un consorcio privado que usa protocolos de confianza. Para centralizarlos, ARSAT debería reemplazar el equipamiento de red (switches y routers) por hardware propio que soporte **DPI (Deep Packet Inspection)** a escala masiva. En este escenario, el IXP deja de ser solo un punto de intercambio de tráfico para convertirse en un **punto de inspección y auditoría**. Todo lo que se intercambia entre un ISP local y el resto del país pasaría por el "filtro" de ARSAT.

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

En estos momentos el centro del entretenimiento esta direccionado en cuanto a multimedia en la plataforma de Youtube que concentra el 90% de los canales de streaming mirados por el publico, generalmente joven del país. En cuanto a películas y series, las plataformas más consultadas son Netflix, Disney Max, Amazon Prime.

En lo que respecta a la investigación o búsqueda por internet, el viejo motor de búsqueda de Google que concentraba casi el total de las consultas esta siendo disputado su dominio por la comunicación directa con chatbots de IA. El tráfico generado por **humanos** representa aproximadamente el **68%** del total de las solicitudes HTTP globales. El **32%** restante es tráfico automatizado (bots), y dentro de este grupo, los **AI Crawlers** (los bots que entrenan o alimentan a modelos como GPT-5 o Claude 4) son la categoría de más rápido crecimiento.

Aún alrededor del **70-75%** de las búsquedas de escritorio se realizan en buscadores tradicionales. Sin embargo, su uso está cayendo (se estima una baja del **25%** para fines de 2026) a medida que los usuarios prefieren respuestas directas. Casi el **60%** de las búsquedas actuales son "zero-click". Esto significa que el usuario obtiene la respuesta directamente de una IA (como las _AI Overviews_ de Google) sin necesidad de entrar a una página web. La interacción directa con modelos de lenguaje representa cerca del **3.2% al 5%** del tráfico de búsqueda total. Esto no solo quita “visitas” a las demás páginas invisibilizando millones de sitios que nunca serán resultado de las búsquedas sino que adempas concentra “el monopolio de la verdad”. Donde el modelo de IA, en este caso Gemini, define la respuesta correcta y se asume, por cotidianeidad, como correcta su respuesta. Una consulta a un LLM consume hasta **30 veces más energía** que una búsqueda tradicional en Google.

Para correr una IA de manera soberana como GPT-4 o Llama-3 en servidores propios, se necesitan **clústeres de GPUs** (como las NVIDIA H100 o A100) o **TPUs**. El fuerte del detacenter de ARSAT no es el cómputo de alta performance (HPC) para entrenamiento de IA. Los racks de servidores de IA consumen hasta 10 veces más energía y generan muchísimo más calor que un servidor estándar. Para hospedar modelos "pesados", ARSAT debería actualizar sus sistemas de **refrigeración** (posiblemente a refrigeración líquida) y su densidad eléctrica.

Para que ARSAT reemplace el uso de modelos extranjeros en el sector público, faltan tres pilares:

- **Hardware Dedicado:** Adquisición masiva de aceleradores de IA (GPUs).
- **Talento Técnico:** No solo ingenieros de red, sino especialistas en _MLOps_ para mantener los modelos corriendo 24/7.
- **Gobernanza:** Reglas claras que obliguen a los organismos públicos a usar la "IA Nacional" en lugar de soluciones fáciles como ChatGPT.

---
#### Referencias

