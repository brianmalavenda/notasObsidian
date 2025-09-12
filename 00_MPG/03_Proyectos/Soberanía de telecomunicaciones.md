tipo:: #mpg/proyectos
tags::
fecha:: 2025-09-12
links::
links_externos::
# ¿Quien controla el internet y como?

## Comunicación de datos cliente - servidor

El internet es la comunicación o flujo de datos desde un nodo servidor a un nodo cliente (PC usuario, celular, tables, etc). Esta comunicación de datos se realiza por paquetes de datos donde ambos nodos son identificados por una dirección IP. En general, los nodos servidores de datos utilizan un pseudónimos de la IP ya que son accedidos por varios nodos clientes. Por ejemplo, una dirección IP de un sitio web como youtube.com podría ser 66.120.192.0, pero su “sobrenombre” es “youtube.com”. Ese nombre de servidor es interpretado, al momento de que un usuario entra a un navegador y escribe en la barra de navegación “https:youtube,com”, por un servidor que tiene los registros de todas las direcciones IP y su sobrenombre DNS (Domain Name Services). Puntualmente una organización centraliza la administración de direcciones IP, registros de nombre de dominio, etc. Esta organización que controla esta información y accesos se llama ICANN (Internet Corporation for Assigned Names and Numbers) y se encuentra en Estados Unidos. Por lo tanto, esta agenda de nombres de dominio, cambia la petición de datos de youtube,com a 66.120.192.0 para buscar el servidor en los múltiples centros de datos a nivel mundial.

Estas direcciones IP son particulares por cada dispositivo, pero en internet, cada IP publicada en la red (distinta al del equipo) es otorgada por el ISP (Internet Service Provider) que es la empresa o compañia que proporciona el internet en determinada zona geográfica. A través entonces de ambos datos, IP del usuario y DNS -> IP servidor, la información se empaqueta y esta lista para circular mediante una códificación de datos generada en sistema binario ( sistema de dos dígitos posibiles, 1 o 0) y se generan paquetes de bits de acuerdo al ancho de banda (ver despues definición) de la conexión. Este paquete tiene una solicitud de información determinada y dirección de origen y destino a groso modo. Cada paquete cumple con una serie de “protocolos” que permite homologar la conexión con cualquier servidor de manera mundial y “habla” el mismo idioma. Estos protocolos son: TCP/IP, http/https o RTP. **Detallaremos mas adelante de ser necesario, ahora …**

## ¿POR DONDE VIAJA?

Esta conexión a los centros de datos puede ser de dos maneras, por cables: coaxial (el de TV), cable de teléfono o de fibra óptica que es el 98% del trafico de internet; y la vía satelital.

La velocidad de la transferencia de datos se mueve a dos tercios de la velocidad de la luz, es decir casi uno 200 mil kilometros por segundo. Para darnos una idea, el diametro de la tierra tiene 13 mil kilometros. Es decir que en un segundo, en un hipotético único cable de que la vuelta al mundo sin mediación, daría casi 15 vueltas en un segundo.

Las principales compañías que controlan el tendido de cables de fibra óptica en el mundo son: AT&T, Google, Verizon y Orange. **[Ver acá empresas que tienen la capacidad de hacer el tendido de cables en el lecho marino]**

Por parte la conexión satélital a través de ondas electromagnéticas. En este caso la principal empresa es Starlink, que controla actualmente Elon Musk junto a las empresas Tesla, Starship y demás. Hay un total de XXX satelites orbitando en la estratosfera destinados a la comunicación de datos, de los cuales ⅔ pertenecen a este monopolio.

[Detallar acá comunicación global, conexión regional y local]

## Red de tendido de cables

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

Hay 4 compañías que fábrican y “ponen” el tendido del cable: Subcom(EE.UU), NEC(Japón), Alcatel submarine networks(Francia) y Hmn Tech [Huawei Marine Networks] (China) .

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image003.png)

En la imagen lo que se ve es que empresas francesas tienen puesto mas de 500 mil km de cable en el lecho submarino. La inmediatamente siguiente, con casi los mismos kilómetros son empresas estadounidenses. Por menos de la mitad le siguen las japonesas y las chinas que en pocos años aumentaron mucho la extensión de cables propios.

Octubre de 2022, un cable que conectaba a Francia desde la costa sur, se rompió??. Al cortarse esa vía de conexión tiene que re-enrutar todos los paquetes que iban por ese camino y volver a “adaptar” toda la conexión lo que afecta al internet de todo el mundo.

El cable que conecta a Francia con Singapur, pasa por el Canal de Suez, surca el Mar Rojo hasta el estrecho de Abe-El Mandeb y costea a India y conecta un punto del sur de China. El cable se llama SeaMeWe-6 undersea cable.

### Bloqueo a Cuba

Tan solo un cable conecta a Venezuela con Cuba y Jamaica (en rojo) de todos los múltiples cables que conectan a todos los países alrededor con el internet de Europa, EE.UU (de mayoría de nodos servidores) y demases países de centro américa y sur america.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

El gobiernon de EE.UU detuvo  la instalación de nuevos cables a través de su ente gubernamental FCC (Federal Communication Commision) [File No. SCL-MOD-20210928-00039]y además bloquear la red “Arcos” de un cable que redea a los países de centroamerica.

### Nodos cercanos a Argentina

[https://elpais.com/politica/2017/04/25/actualidad/1493085612_857382.html](https://elpais.com/politica/2017/04/25/actualidad/1493085612_857382.html)

## Estándares de red

Redes de 5 ghz (redes n o a o ac) y redes de 2.4 ghz (redes b o g) . No todos los dispositivos son compatibles con las redes de 5 ghz, por eso muchos de los routers ahora tienen señal dual para tratar de salvar esta falta de compatibilidad.

La IEEE llama al Wifi 802.11 por ejemplo, y puede ser a, b, ac, etc.

Hay que tener en cuanta que cada tipo de red tiene una capa de canales por donde viaja la data. Las redes de 2.4 ghz o las redes b/g tiene 11 canales y tienen overlap entre canal y canal mientras que las 5 ghz tiene 24 y con una configuración inteligente del router. Los celulares android tiene la posibilida de instalar una app que se llama Wifi analyzer para ver el mapa de radiofrecuencias y canales usados en la zona.

## Seguridad de red

Los tipos de seguridad son WEP, WPA, WPA-2.

WEP: La contraseña son en hexadecimal. Si tu contraseña no pasa de la F, es una red WEP. Las WPA son mejor, las WAP-2 mejor ganandole a hackeos pero igual le hicieron un ATACK KRACK

Formas de dificultar el hackeo es poniendo nombre a la red, por ejemplo “pepito”. Eso sería configurar el SSID. Lo otro es la conexion “fisica” por WPS. Lo otro es una white list de MAC seguras a las cuales les permitis conectarte al router. Si te copian la MAC del dispositivo (Mac spoofing), pc, celular etc, esto se vuelve la peor vulnerabilidad.

 

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image005.png)

## Control del ancho de banda de los cables

En general, antes el ancho de banda era controlado por las proveedoras de internet. Pero actualmente mas del 70% del ancho de banda lo controlan los monopolios del software y telecomunicaciones: Google, Amazon, Facebook ..

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)

## FABRICANTES DE FIBRA OPTICA

En el mundo interconectado de hoy, la demanda de Internet confiable y de alta velocidad es cada vez mayor. Esta demanda impulsa el crecimiento de las empresas que fabrican cables de fibra óptica, la columna vertebral de la infraestructura de comunicaciones moderna. Los cables de fibra óptica brindan una velocidad y un ancho de banda inigualables, lo que los hace esenciales para diversas aplicaciones, desde servicios de Internet residencial hasta centros de datos y redes de telecomunicaciones a gran escala. Aquí, exploraremos algunas de las principales empresas de la industria de fabricación de cables de fibra óptica.

**Principales fabricantes de cables de fibra óptica**

Las 5 empresas más grandes que fabrican cables de fibra óptica

#### 1.Corning Incorporada

Corning Incorporated, pionero en fibra óptica, ha estado a la vanguardia de la industria durante más de cuatro décadas. Ofrecen una amplia gama de soluciones de conectividad, cables y fibra óptica. Es una empresa yanqui.

[https://www.corning.com/cala/es/innovation/culture-of-innovation/the-history-of-corning-innovation.html](https://www.corning.com/cala/es/innovation/culture-of-innovation/the-history-of-corning-innovation.html)

#### 2. Grupo Prysmian

Prysmian Group es uno de los principales fabricantes mundiales de cables de energía y telecomunicaciones. Con una amplia cartera, prestan servicios a una amplia gama de industrias, incluidas las de telecomunicaciones, energía y construcción.

Es una empresa multinacional con sede en Milán, Italia, especializada en la producción de cable eléctrico para uso en el energía y telecom sectores y para fibras ópticas. La empresa se originó en 1879 como Pirelli Cavi e Sistemi. En 1881, consiguió un contrato para producir cables telegráficos submarinos ingeniería militar. En 2005, Prysmian Srl fue creada por Goldman Sachs después de adquirir Pirelli & Negocio de Cables y Sistemas de CSpA. En diciembre de 2017, compró la empresa estadounidense (y rival) Cable general basado en Kentucky. Una transacción de casi tres mil millones de dólares, compuesta por capital (1,5 mil millones) y deuda financiera. Se fusionó la empresa líder mundial en fabricación de cables en términos de ingresos (Prysmian) y la cuarta (General Cable). Para 2021, Prysmian confirmó el lanzamiento del nuevo buque tendido de cables Leonardo da Vinci[23] que será el más largo del mundo con 171 metros.

#### 3.Electricidad Furukawa

Furukawa Electric es una empresa japonesa con fuerte presencia en el mercado mundial de fibra óptica. Proporcionan soluciones de cable y fibra óptica de alto rendimiento para diversas aplicaciones.

#### 4.Industrias Eléctricas Sumitomo

Sumitomo Electric es un actor importante en la industria de la fibra óptica, conocido por su tecnología avanzada y productos de alta calidad. Atiende tanto al mercado industrial como al de telecomunicaciones.

#### 5.Nexanos

Nexans es líder mundial en la industria del cable y ofrece una amplia gama de soluciones de fibra óptica. Se centran en proporcionar soluciones innovadoras y sostenibles para sus clientes.

## Microchips

Se fabrican mediante un ecosistema global: AMD, Qualcomm, Intel, Apple y Nvidia destacan en el diseño de chips. Synopsys y Cadence crean herramientas y software sofisticados de diseño asistido por computadora sobre los cuales los fabricantes de chips trazan sus nuevas ideas. Applied Materials crea y modifica los materiales para forjar los miles de millones de transistores y cables de conexión en el chip. ASML, una empresa holandesa, proporciona las herramientas de litografía en asociación con, entre otras, Carl Zeiss SMT, una empresa alemana especializada en lentes ópticas, que dibuja los moldes en los wafers de silicio a partir de esos diseños. Lam Research, KLA y empresas de Corea del Sur, Japón y Taiwán también juegan papeles clave en esta coalición.

## Detalle de paquetes de datos

TCP/IP: Datos de transporte

http/https: Acceso a web’s

RTP: Streaming de video en vivo y llamadas de voz

# INDUSTRIA DEL SOFTWARE EN ARGENTINA

A inicios de la década pasada, el acceso a internet en Argentina mostraba signos de concentración de mercado, desinversión, retraso tecnológico y disparidad geográfica en la penetración, calidad de los servicios y valores de interconexión. Solo el 30 % de los hogares tenían acceso a internet de banda ancha; la Ciudad Autónoma de Buenos Aires tenía niveles de penetración del 45 % mientras que el resto del país no superaba el 15 % (Argentina Conectada 2011) . Esta situación era agravada por la falta de capacidad de los organismos regulatorios (Abdala 2001; Bergara y Pereyra 2006; Mastrini y Aguerre 2007; Galperin y Cabello 2008) para controlar la posición dominante en la fijación de precios de interconexión y venta de capacidad al entramado de cooperativas y pymes que dan servicio de internet (ISP) en las zonas no rentables para el operador mayorista. En ese momento, existían principalmente seis operadores mayoristas: Telecom, Telefónica, Claro, GigaRed, Global Crossing (Level 3) y Fibertel, cuya posición imponía valores de mega mayoristas altos y disímiles (Baladrón 2018), que hacían prohibitivo el acceso a internet (Aguerre y Galperín 2015, 11) para la población en general y otros operadores, a pesar de los intentos regulatorios.

se instruyó a ARSAT la construcción y operación de la REFEFO; la fibra tendida quedaba bajo su propiedad. El diseño original de la red sumaba 58.000 km (Argentina Conectada 2011) en tres niveles:

1) 22 000 km de redes troncales de fibra óptica sobre rutas nacionales y trazas ferroviarias, para transporte de datos de larga distancia nacional.

2) 25 000 km de redes provinciales de fibra óptica sobre rutas provinciales, para ampliar la capilaridad hacia localidades del interior.

3) Redes de última milla desde el nodo hasta el hogar, tendidas según el catastro.

La construcción de nuevos tendidos se ejecuta en fases. Primero, se realiza la obra civil de zanjado y canalización adyacente a la ruta. Allí, se entierra el tritubo por donde pasa fibra óptica oscura. Por último, al costado de las rutas y en la entrada de cada localidad, se instalan “nodos”: un contenedor con equipamiento de iluminación de la fibra óptica e interconexión con los ISP. Otras alternativas son la compra de fibra existente o la firma de acuerdos irrevocables de uso (IRU) con otros operadores; esto ahorra la obra civil, pero requiere la independización de ductos y/o iluminación, según el caso. Al momento de redactar este trabajo, la REFEFO tiene casi 32.000 km en funcionamiento.

Durante este período, el valor de venta mayorista registró una caída del 50 %, en promedio, en las zonas donde llegó ARSAT (Convergencia Latina 2017), a partir de una fijación de precio de venta de megabytes a ISP locales en $447 pesos (Larocca 2019).

Este despliegue territorial estratégico, junto con la concentración de facultad regulatoria en el Ejecutivo nacional, fue visto como una “amenaza” por los operadores privados (Baladrón 2018; Signals Telecom 2013); la presión competitiva en punto de alta concentración y elevados costos de capital (Galperin 2013) podrían afectar la rentabilidad del sector en ese segmento, vía exclusividad geográfica y/o competencia de ARSAT. Entrevistados de las empresas de telecomunicaciones manifestaron que si bien tenían reservas sobre la operación de la red, observaron en ARSAT capacidad asociada a la disponibilidad de recursos, facilidad de obtención de permisos y competencia de los equipos técnicos, por ello, creían que la REFEFO sería finalizada (Izaguirre 2022). Al mismo tiempo, tenían interés en que el Estado realizara un adelanto de inversión para actualizar la tecnología y la extensión de redes (Baladrón 2018).

En consecuencia, a fin de evitar la potencial exclusión y poder acceder a beneficios selectivos, en 2011, antes de que iniciaran las obras, se celebraron acuerdos de intercambio de infraestructura entre ARSAT y los grandes operadores Telecom, Telefónica y Claro Argentina, bajo la figura de acuerdos irrevocables de uso (IRU) (Baladrón 2018), que no Revista Latinoamericana de Economía y Sociedad Digital, Issue Especial 2 Página 17 de 37 hubieran sido posibles sin la credibilidad en ARSAT para finalizar dichas obras.

En 2015, con el 90 % de la obra civil terminada, pero antes de que iniciara la iluminación de la red, los acuerdos con Claro y Telefónica se renegociaron como contratos de venta de capacidad en determinadas trazas (Clarín 2016; Télam 2016; Baladrón 2018). Una vez más se observa cómo la capacidad y antecedentes de cooperación otorgaron credibilidad para repetir negociaciones y arribar a nuevos acuerdos. [espectro radioeléctrico asignado a ARSAT]

Así, para utilizarlos en la REFEFO, se creó el Programa de Conectividad, mediante la Resolución ENACOM N°3597/2016, acorde a los objetivos del fondo y la presentación de proyectos de ARSAT para la instalación de 770 nodos de acceso a internet en cada localidad. Esto implicaba, lógicamente, la puesta en funcionamiento operativo de la red, en tanto en los nodos se instalaba el equipo de iluminación.

El financiamiento restante se empleó en el programa de última milla, creado por Resolución ENACOM N° 8955/2016 y 2899/2018. Se financiaron 225 proyectos a ISP locales, para tendidos de redes al interior de las manzanas de cada pueblo (ENACOM 2019). Esto produjo retroalimentación de beneficios; la REFEFO era la condición de acceso mayorista a ISP en zonas sin cobertura y, al mismo tiempo, la venta de capacidad mayorista a pequeños operadores diversificaba la autosustentabilidad de la red de ARSAT, alcanzando a 787 operadores locales (ARSAT 2022a). Durante la pandemia de SARS-CoV-2, hubo un aumento del 84 % sobre el tráfico de la red, 660 ISP pidieron más conectividad y 168 aguardaron factibilidad técnica sobre la solicitud de capacidad a adquirir (ARSAT 2022c). Esto demandó la actualización tecnológica del equipamiento de iluminación, para triplicar la velocidad de transmisión y aumentar la cantidad de los megabytes que podían proveerse a los ISP. El 98 % de los fondos ejecutados del FSU en 2020 fueron para este proyecto (ENACOM 2021).

El Plan Argentina Conectada contemplaba 25 000 km de estas redes, las cuales se conectarían con las troncales de ARSAT para extender fibra óptica hacia localidades del interior de cada provincia. Las empresas se encargarían de la construcción y operación de la red, pero en caso de no estar conformadas, ARSAT realizaría los tendidos. La propiedad de los tendidos era compartida, las provincias cedían a ARSAT ductos y/o pelos de fibra óptica de las redes a construir.

## TRONCALES SUBMARINOS DE INTERNET

A la Argentina llegan seis cables submarinos. Cuatro de ellos, provienen originalmente de distintos países, y tocan diversos puntos de América del Sur, con paso obligado por Brasil y Uruguay, desde la costa este. Ellos son: Telxius, South American Crossing o SAC, de Cirion Technologies; Malbec, de Globenet,; y Tannat. Los cables Unisur y Bicentenario(o Malbec) unen de manera directa Uruguay con la Argentina. Los cables Tannat y Malbec conectan a estos dos países desde dos puntos distintos provenientes desde Brasil.

### Telxius (Telefónica)

Telefónica, a través de su empresa Telxius, también es un actor relevante en el panorama de los cables submarinos. Aunque el texto no detalla específicamente cables de Telxius conectando directamente con Argentina, Telefónica es una empresa con una fuerte presencia en Latinoamérica y es propietaria de infraestructura de cables submarinos que contribuyen a la red global. La participación creciente del gobierno español en Telefónica destaca la importancia estratégica de estas infraestructuras, aunque no esté directamente vinculada a los cables submarinos.

#### SAM-1

El sistema de cable SAm-1 es de propiedad privada y está operado por Telxius. SAm-1, con una longitud de casi 25.000 km, es un anillo de cable submarino de fibra óptica tendido sobre el lecho marino que rodea Latinoamérica. Es el quinto sistema de cable submarino más largo del mundo y tiene una capacidad actual de 20 Tbps. Cuenta con extensiones de fibra óptica a Ecuador, Colombia y República Dominicana, además de las redes troncales terrestres transandinas y transguatemaltecas.

Interconecta las costas americanas del Caribe, el Atlántico y el Pacífico, con diversos puntos de desembarque. Su punto más septentrional se encuentra en Boca Ratón (Florida, EE. UU.) y el más meridional, en Valparaíso (Chile) y Las Toninas (Argentina).

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image008.jpg)

#### Firmina (Google)

Google anunció la llegada del cable submarino Firmina a Argentina, que conecta Estados Unidos (Carolina del Sur) con Argentina (Las Toninas). Este cable, que también conecta Brasil (Playa Grande) y Uruguay (Punta del Este), busca mejorar la conectividad y reducir la latencia en la región, permitiendo que los datos viajen más rápido.

La compañía informó, justamente, que este cable se construyó con el objetivo de mejorar el alcance y la resiliencia de la red dentro de América del Sur, y entre ésta y América del Norte. Aseveró, además, que junto con Tannat, el cable submarino que comparte con Antel en Uruguay, “habrá más que duplicado la capacidad potencialmente accesible de los cables submarinos en la Argentina, mejorando la conectividad en el país”, expresó Google a través de un comunicado. Agregó que hacia 2027 el impacto en la economía de los países será de 23.700 millones de dólares sobre el PBI, tal como resulta de un estudio realizado por Analysis Mayson. Se trata de un proyecto del que también participa **Telxius**, de Telefónica, como modo de robustecer la Internet que se sirve en estos países.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image009.png)

#### Mistral

El cable del Pacífico Mistral es el primero en conectar Puerto San José (Guatemala) con Valparaíso (Chile), con puntos de amarre adicionales en Salinas (Ecuador), Lurín (Perú) y Arica (Chile) desde 2001.

El cable del Pacífico Mistral, en el que Telxius colabora junto con América Móvil, tiene una capacidad potencial de 132 Tbps, así como la latencia más baja del mercado entre Guatemala y Chile. Como resultado, los 7.300 km de cable proporcionan, a toda la costa del Pacífico de Sudamérica, los más altos niveles de servicio, fiabilidad y seguridad.

Este cable forma parte del compromiso continuo de los dos grupos de telecomunicaciones más grandes de Latinoamérica para mejorar las comunicaciones a nivel mundial.

La idea de posicionar a Chile como un hub de conectividad en el Pacífico toma sentido considerando también el interés de grandes empresas por desarrollar procesos en el territorio, como ocurre con Amazon Web Services, que instalará ocho estaciones satelitales en Punta Arenas, o Google, que ya cuenta con un datacenter en Quilicura y el Cable Submarino Curie, que en 2019 permitió conectar a California con Valparaíso.Cristian Ramos, gerente de Desarrollo de Infraestructura para América Latina de Google, señala que hubo una “clara elección” cuando la empresa detrás del motor de búsqueda decidió instalar este tipo de tecnología en el país.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image011.jpg)

#### Tanant

Con una longitud de 2.000 kilómetros, se suma a Brusa (Virginia Beach – Río de Janeiro) y Junior (Río de Janeiro – Santos) en la costa atlántica de América Latina para ofrecer la conectividad de extremo a extremo más moderna y diversa entre Estados Unidos, Brasil y Argentina.

Con estos tres cables submarinos de nueva generación, que dan redundancia al SAm-1, Telxius proporciona una infraestructura esencial que permite a América Latina satisfacer las necesidades tanto de empresas como de personas y facilita que la región pueda participar plenamente en la economía digital global actual.

Tannat, a través de Junior y Brusa, se conecta directamente al centro de datos de Virginia Beach, desde donde cuenta con un backhaul directo a Ashburn (EE. UU.), el área con la mayor concentración de centros de datos del mundo. Además, el centro de datos de Virginia Beach proporciona acceso directo a Marea y Dunant, creando un anillo global que une Latinoamérica, Estados Unidos, Europa y el resto del mundo.

#### Dunant

Con 6.600 km de longitud, Dunant es un cable transatlántico de nueva generación capaz de canalizar el enorme incremento del tráfico de datos y contenidos entre Europa y los Estados Unidos.

Dunant se suma a Marea para ofrecer una propuesta transatlántica inigualable y constituye una de las rutas de menor latencia y mayor capacidad a nivel mundial. La combinación de ambos sistemas permite a Telxius conectar ambos lados del Atlántico con los dos cables más modernos en una ruta transatlántica única, diversa y mucho más al sur que el resto de cables desplegados en el Atlántico Norte.

La propuesta transatlántica de Telxius conecta directamente Ashburn, Richmond y Virginia Beach en los Estados Unidos con París, Madrid y Derio (Bilbao) en Europa, proporcionando acceso directo a las áreas con la mayor concentración de centros de datos del mundo y nodos de conectividad clave.

#### Marea

Con 6.600 kilómetros de longitud y 200 Tbps de capacidad, Marea ha revolucionado las comunicaciones transatlánticas, con una altísima capacidad y baja latencia, que podrán seguir evolucionando al ritmo de la tecnología más avanzada.

A día de hoy es uno de los cables de mayor capacidad del mundo. Su trazado, mucho más al sur que otros cables en el Atlántico Norte, contribuye a salvaguardar la seguridad de las comunicaciones a través del Atlántico, y su diseño flexible e innovador hará que pueda adaptarse fácilmente al futuro de los servicios de datos. Además, Marea se suma a Dunant para ofrecer redundancia de red en esta estratégica ruta.

Marea conecta Virginia Beach (EEUU) con Sopelana (España), proporcionando la ruta de más baja latencia entre EEUU y el sur de Europa. Además, facilita una ruta alternativa hacia los principales hubs de comunicaciones de África, Oriente Medio y Asia.

El centro de datos de Virginia Beach, que es punto de amarre de los cables Marea y Dunant, cuenta con un backhaul directo hacia Ashburn —donde se encuentra la mayor concentración de centros de datos del mundo—, así como hacia Richmond.

Al otro lado, en Europa, Marea se conecta desde Sopelana al centro de datos de Derio que ha sido especialmente diseñado para canalizar la capacidad de este cable y llevarla a los principales nodos de comunicaciones de Europa.

### Facebook (Microsoft)

Facebook está construyendo un cable submarino de 2.500 km con varios objetivos, siendo uno de ellos impulsar la velocidad de Internet en Argentina.

Se ha asociado con el operador de infraestructura de telecomunicaciones GlobeNet para construir un nuevo enlace entre Brasil y Argentina, tal y como se muestra en una de las imágenes. Globenet ya cuenta con 23.500 kilómetros en América, y ahora es cuando ha hecho pública la participación de Facebook en este nuevo tramo.

El cable tiene nombre: “Malbec”, y conectará a Buenos Aires con São Paulo y Río de Janeiro en Brasil, con un punto en Porto Alegre.

Aunque dicho cable está diseñado para conectar mejor a Brasil con Argentina, servirá para mejorar la conectividad entre los Estados Unidos y el segmento sur de América del Sur, incluida Argentina. La mayoría de los servidores de Facebook están en Estados Unidos, por lo que invertir en cables es fundamental para mejorar la velocidad y las transferencias de datos entre sus servidores principales y Argentina.

Facebook ya participó en 2012 en el cable submarino de Asia Pacific Gateway, que conecta Malasia, Corea del Sur y Japón, entre otros países de la región, y el año pasado Facebook y Microsoft completaron su cable transatlántico de Internet Marea, que conecta América del Norte con Europa continental. Comentan en WhatsNew que se espera que el nuevo cable Malbec esté operativo para el año 2020, y las compañías dicen que “duplicará la capacidad internacional actual” entregada a Argentina.

#### Globenet

GlobeNet es un cable de telecomunicaciones de submarino que conecta a Estados Unidos., Bermudas, Colombia, Venezuela y Brasil. Tiene una capacidad de 1.36 Tbit/s montada sobre cuatro pares de fibra (34 x 10 Gigabit x 4 pares de fibra). Se demostró en 2010 que el sistema de cable fue capaz de soportar 5.76 Terabits en todos los segmentos construidos en 2001. El sistema de cable era inicialmente desplegado y llamado GlobeNet, la cual es hoy en día una compañía de BTG Pactual. El 7 de mayo de 2018, GlobeNet anunció el despliegue de un nuevo cable submarino de 2500 km que unirá Río de Janeiro y Sao Paulo con Buenos Aires. Esta nueva infraestructura conectará a Argentina con la red de GlobeNet en Brasil, enlazando efectivamente el Cono Sur de América con los Estados Unidos y el resto del mundo

**Socios**

**Centro de datos y redes:** ARISTA, Avocent, Barracuda

**Seguridad electrónica:** Axis, Bosch, Digital Watchdog

**Infraestructura móvil y radio:** Agilent Tecnologies, AJ ingenieros, American Racks

**Software:** Allot, Okta, EgnYte

**Energía:** APC, C, EATON

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image013.jpg)

Dos nodos en Chacarita y Pacheco con operadores que extendieron la fibra oscura desde Las tóninas. Infraestructura propia en el Pop.

### Cirion Tecnologies

(anteriormente Lumen Tecnologies, empresa estadounidense que vende troncal de America Latina)

Conectamos tu empresa con los datos, las apps y las personas indicadas para elevar tus negocios al máximo nivel. Te brindamos una conexión a internet de máxima calidad, velocidad y seguridad.

Conjunto completo de opciones escalables de internet de alta velocidad a través de conexiones Ethernet desde puertos de 10/100 Mbps hasta puertos de 100 Gbps.

Red troncal que conecta más de 20 países en América Latina y Estados Unidos, proporcionando conectividad crítica de extremo a extremo con más de 120 Tbps de capacidad de IP, incluidos más de 48 Tbps de capacidad de interconexión.

#### SAC

El sistema de cable South American Crossing (SAC) incluye una red de anillos de fibra óptica terrestre y submarina de aproximadamente 12.000 millas de ruta (20.000 km).

El sistema de cable SAC forma un anillo de cuatro pares de fibras utilizando tecnología de multiplexación por división de longitud de onda densa (DWDM).

El sistema de cable SAC fue construido por Global Crossing y estuvo listo para entrar en servicio en septiembre de 2000. En 2015, Level 3 añadió una extensión al sistema de cable SAC con un nuevo ramal a una estación de cable en Punta Bazán, Buenaventura, en la costa pacífica colombiana, cerca de Cali. El nuevo ramal de SAC Colombia tiene una capacidad de diseño de 4,5 Tbps y una capacidad inicial de 400 Gbps.

Los segmentos terrestres del sistema de cable SAC conectan con la mayoría de las principales ciudades de Sudamérica, como Río de Janeiro, São Paulo, Buenos Aires, Santiago, Lima, Cali y Bogotá. La red de anillos SAC se completa en su extremo sur con un enlace terrestre a través de los Andes entre Las Toninas y Valparaíso .

El sistema de cable SAC ahora es parte de los activos de red de CenturyLink, así como Mid-American Crossing ( MAC ), Pan-American Crossing ( PAC ), Atlantic Crossing 1 ( AC-1 ),  AC-2/Yellow , Hawaii Island Fiber Network (HIFN), etc.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image015.jpg)

### Algar Telecom

Algar Telecom es una empresa brasileña de telecomunicaciones con presencia en los estados de Goiás, Mato Grosso do Sul, Minas Gerais, Paraná, Río de Janeiro, Río Grande do Sul, Santa Catarina, São Paulo y también en el Distrito Federal. Es el único operador que se mantuvo privado, incluso tras la creación de Telebrás durante el régimen militar, y se caracteriza por ser la quinta mayor empresa del sector de las telecomunicaciones. Atiende a más de un millón cuatrocientos mil clientes, entre particulares, microempresas y pequeñas empresas, clientes corporativos y operadores.

### Telecom

Sparkle, la unidad de redes de transmisión y cables submarinos de Telecom Italia, emerge como un actor principal en la conectividad de América Latina, incluyendo Argentina. Su presencia en la región es significativa, operando una extensa red troncal global que abarca más de 600.000 km de fibra, extendiéndose desde Europa hasta África, América y Asia. En particular, Sparkle posee el "backbone americano", una infraestructura que combina redes terrestres y submarinas para conectar Sudamérica, el Caribe, México y Estados Unidos. Esta vasta red es crucial para la conectividad de Argentina con el mundo.

### ASN (Nokia, Gobierno Francés)

ASN (antes Alcatel Submarine Networks), ahora propiedad del gobierno francés tras ser adquirida a Nokia, es otro jugador crucial en este mercado. Aunque el texto menciona su actividad principalmente en el Caribe y proyectos como SAC, Deep Blue One, CSN-1 y AMX-3/TIKAL, ASN es un proveedor global de infraestructura de cables submarinos. Su experiencia en diseño, fabricación, instalación y mantenimiento de estas redes la convierte en un actor relevante en el ecosistema global que sustenta la conectividad de Argentina.

### Otros Consorcios y Empresas

Más allá de estas empresas principales, la infraestructura de cables submarinos suele ser construida y operada por consorcios que agrupan a diversas empresas de telecomunicaciones, proveedores de contenido y fondos de inversión. Estos consorcios permiten compartir los costos y riesgos de proyectos de gran envergadura. Empresas como Google, Facebook, Amazon, y grandes operadores de telecomunicaciones como Verizon, Deutsche Telekom, Orange, Vodafone, Level 3 (ahora Lumen), y Tata Communications, mencionadas en el texto en el contexto de cables transatlánticos y transpacíficos, también participan activamente en la red global que indirectamente beneficia la conectividad de Argentina.

Capa       Material Función

1             Polietileno            Aislamiento exterior

2             Cinta tipo Mylar   Protección mecánica

3             Cables Trenzados de Acero              Resistencia a la tensión y protección contra cortes

4             Aluminio                Barrera contra la humedad

5             Policarbonato       Protección adicional

6             Tubo de Aluminio o Cobre Conductor eléctrico (en algunos diseños) y protección

7             Vaselina (Petroleum Jelly) Barrera contra la humedad y relleno

8             Fibra Óptica          Núcleo de transmisión de datos

Estos cables son capaces de transmitir enormes cantidades de datos a velocidades impresionantes. Un solo hilo de fibra óptica puede transmitir 3.840 gigabits por segundo, equivalente al contenido de 102 DVDs cada segundo. Cables con múltiples pares de hilos de fibra pueden alcanzar capacidades de transmisión superiores a los 60 terabits por segundo.

##### REFERENCIAS

[https://www.submarinenetworks.com/en/](https://www.submarinenetworks.com/en/)

[https://telxius.com/](https://telxius.com/)

[https://www.ciriontechnologies.com/es-ar/](https://www.ciriontechnologies.com/es-ar/)

[https://www.telesemana.com/](https://www.telesemana.com/)

Cessi: [https://cessi.org.ar/opssi/](https://cessi.org.ar/opssi/)

[https://cessi.org.ar/wp-content/uploads/2024/11/CESSI-Reporte-del-Sector-del-Software-y-Servicios-Informaticos-OCT-2024.pdf](https://cessi.org.ar/wp-content/uploads/2024/11/CESSI-Reporte-del-Sector-del-Software-y-Servicios-Informaticos-OCT-2024.pdf)

cirion: [https://www.ciriontechnologies.com/es-ar/](https://www.ciriontechnologies.com/es-ar/)

arsat: [https://www.arsat.com.ar/](https://www.arsat.com.ar/)

comunitel : [https://comunitel.com.ar/](https://comunitel.com.ar/)

redes subterráneas: [https://dplnews.com/argentina-95-de-la-refefo-esta-iluminada-se-triplico-su-capacidad-en-3-anos/](https://dplnews.com/argentina-95-de-la-refefo-esta-iluminada-se-triplico-su-capacidad-en-3-anos/)

proveedores de internet: [https://www.argentina.gob.ar/sites/default/files/isp_por_provincias_2.pdf](https://www.argentina.gob.ar/sites/default/files/isp_por_provincias_2.pdf)

## OPERADORES DE INTERNET ARGENTINA

En Argentina, el mercado de internet fijo se concentra en pocas empresas. Las principales son Fibertel (Grupo Clarín), Arnet (Telecom) y Speedy (Telefónica), que atienden al 85% de los clientes. Telecentro, que ahora es una empresa independiente, y Uol Sinectis (fusionada con Sion) se encuentran más atrás.

El mercado de internet fijo en Argentina se caracteriza por la concentración de clientes en pocas empresas:

Fibertel, Arnet y Speedy:

Estas tres empresas dominan el mercado, con Fibertel y Arnet en la vanguardia.

Telecentro:

Tras el quiebre de Grupo Arnet y la fusión de Telecom con Movistar, Telecentro se mantiene como un jugador importante, pero con una menor participación que las tres principales.

Uol Sinectis (y su fusión con Sion):

La fusión de Uol Sinectis con Sion ha reducido su presencia en el mercado, aunque siguen siendo relevantes en algunos sectores.

Otras empresas:

Además de las mencionadas, existen otras empresas y cooperativas que también brindan servicios de internet, como las que integran CABASE.

La cantidad de usuarios de internet en Argentina:

En enero de 2024, Argentina tenía 40,58 millones de usuarios de internet, lo que representa el 88,4% de la población.

La tasa de penetración de internet en Argentina es alta, pero existen brechas de acceso entre diferentes grupos poblacionales, como personas mayores y residentes en zonas rurales.

El uso de dispositivos móviles es muy común entre los usuarios de internet, con casi el 98% utilizando teléfonos celulares y smartphones.

El uso de computadoras (laptops y PC) y tablets también es significativo, aunque en menor medida que los teléfonos.

El comercio electrónico en Argentina ha experimentado un crecimiento notable en los últimos años, con más de 28 millones de usuarios realizando compras en línea.

### Telefónica / Telxius

Operan el cable submarino South America-1 (SAm-1), que conecta Estados Unidos con América del Sur. Telxius, filial de Telefónica, gestiona una extensa red de cables submarinos y terrestres en la región.

### Level 3 / CenturyLink

Administran el sistema South American Crossing (SAC), una red de cables que interconecta América Latina.

### Telecom Argentina / Antel (Uruguay)

Participan en el cable Bicentenario, que enlaza Argentina y Uruguay, facilitando la conectividad regional. Brinda soluciones de internet fibra óptica para empresas, con servicios como internet simétrico y alta velocidad.

### Google

Ha desplegado el cable submarino Firmina, que conecta Las Toninas directamente con Myrtle Beach, EE. UU., pasando por Brasil y Uruguay.

### GlobeNet

Opera el cable Malbec, en colaboración con Facebook, mejorando la conectividad entre Argentina y Brasil.

### Metrotel y Silica Networks

Han invertido en el tendido de nuevas rutas de fibra óptica desde Las Toninas hacia Buenos Aires, mejorando la infraestructura nacional. Silica se especializa en transporte de fibra óptica y servicios de conexión, participando en proyectos como el Proyecto Fibra Óptica Austral (FOA).

Tiene 5 puntos de cruce de la cordillera desde Chile y las redes que van a Valparaiso supongo.

### Sion e iPlan

Colaboran con Movistar para ampliar la cobertura de fibra óptica en el país, fortaleciendo la red de distribución de Internet. IPlan ofrece Internet Fibra con alta velocidad y cobertura a nivel nacional. Se destaca por ofrecer internet de alta calidad y servicios IT para empresas. Sion brinda servicios de internet con fibra óptica para el hogar.

Opera en CABA: Recoleta, Nuñez, Villa Crespo, Puerto Madero, Balvanera, Barrio Fraga, Retiro, Palermo, Villa Urquiza, Belgrano, Constitución, Caballito, Barrio 31, Colegiales, San Cristóbal, Parque Chacabuco, Flores, Monserrat, Almagro, San Nicolás.

GBA: Olivos, Vicente López

Iptel: Ofrece internet fibra óptica para empresas, con opciones de IP fija y otros servicios.

### Arsat

Implementó la Red Federal de Fibra Óptica para mejorar la conectividad en todo el país, con nodos para que se conecten proveedores locales.

Linkear: Es una empresa local que ofrece internet banda ancha y fibra óptica en el partido de Moreno, Buenos Aires.

Igu Networx: Ofrece servicios de telecomunicaciones, incluyendo internet.

Emblue: Brinda servicios de internet, incluyendo opciones para empresas.

### Empresas con proveduría para revendedoras de internet

Cirion Tecnologies

## NUESTRO ISP

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image017.jpg)

Peering publico conocido: Kbase (CDN?)

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image019.jpg)

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image021.jpg)

1-       Inscripción a ENACOM

2-       Inscripción en LACNIC con ASN propio

3-        

El armado de un ISP (Internet Service Provider) es un nodo que contrata los servicios de un proveedor de intenet que dispone de una red troncal con gran cobertura geográfica que garantiza velocidad y sostenimiento del servicio. Esos proveedores mayoristas disponen al mercado una cantidad de megabytes del ancho de banda que tienen para contratarlo de manera privada personal o privada “empresarial”. Un ISP contrata una cantidad de megabytes que luego “subcontrata”, alquila, a personas individuales.

### Inscripción a ENACOM

La matrícula en ENACOM (Ente Nacional de Comunicaciones) generalmente se refiere a la inscripción en diferentes registros que ENACOM administra, como el Registro Único de Personas Responsables de Servicios de Comunicación (RUPECO) o el Registro de Agencias Publicitarias. Estos registros son necesarios para realizar actividades relacionadas con las telecomunicaciones y las comunicaciones masivas.

**¿Cómo inscribirse en los registros de ENACOM?**

La inscripción en los registros de ENACOM, como RUPECO, generalmente se realiza a través de la plataforma Trámites a Distancia (TAD). El proceso puede variar dependiendo del registro específico, pero generalmente implica:

Acceder a la página web de ENACOM: Visitar el sitio oficial de ENACOM (enacom.gob.ar).

Buscar el registro específico: Identificar el registro en el que se requiere inscribirse, como RUPECO o el Registro de Agencias Publicitarias.

Descargar la documentación necesaria: Puede ser necesario descargar formularios o documentos específicos para el registro.

Completar y presentar la documentación: Completar los formularios y adjuntarlos a través de la plataforma TAD, junto con la documentación respaldatoria.

Realizar el pago de la tasa (si aplica): Algunos registros pueden requerir el pago de una tasa de inscripción.

Seguir el trámite: Una vez iniciada la solicitud, es posible seguir el trámite a través de la plataforma TAD.

### Inscripción en LACNIC con ASN propio

Un ASN (Autonomous System Number) propio es un número único que identifica de manera exclusiva tu red en Internet. Es como una matrícula que te permite controlar cómo se enruta el tráfico de tu red y cómo te interconectas con otras redes, especialmente con un protocolo llamado BGP (Border Gateway Protocol). LACNIC, el Registro Regional de Internet para América Latina y el Caribe, es la entidad que gestiona estos ASN para la región.

¿Por qué necesitas un ASN propio?

Control del enrutamiento:

Con un ASN, puedes decidir cómo se dirige el tráfico de tu red, tanto dentro como fuera de ella. Esto es crucial para optimizar el rendimiento, la seguridad y la eficiencia de tu red.

Interconexión con otros AS:

Un ASN te permite conectar tu red con otras redes en Internet, utilizando protocolos como BGP, que permite el intercambio de información de enrutamiento entre diferentes sistemas autónomos.

Identificación única:

El ASN es un identificador global único para tu red, lo que evita confusiones y garantiza que tu tráfico se enruta correctamente.

Mayor control sobre tu red:

Un ASN te da mayor control sobre tu red, lo que es especialmente importante para empresas y proveedores de servicios de Interne

**Referencias**

### Claro le pidió al Gobierno que se deje sin efecto la venta de Telefónica a Telecom

[https://www.lanacion.com.ar/economia/claro-le-pidio-al-gobierno-que-se-deje-sin-efecto-la-venta-de-telefonica-a-telecom-nid01042025/](https://www.lanacion.com.ar/economia/claro-le-pidio-al-gobierno-que-se-deje-sin-efecto-la-venta-de-telefonica-a-telecom-nid01042025/)

Claro también se presentó la semana pasada como testigo ante el Tribunal Nacional de Defensa de la Competencia. “Si se aprueba la transacción, Telecom tendría más del 70% del mercado de telefonía móvil en la ciudad de Buenos Aires y el AMBA y 30 puntos más de share en internet al hogar en todo la Argentina, es decir, 5 millones y medio de hogares a los que les puede acercar una oferta convergente”, explicó Alejandro Quiroga Lopez, director de Asuntos Regulatorios e Institucionales de Claro Argentina, Uruguay y Paraguay, y sumó que “va a haber 300 localidades de interior país con concentración, lo que es un problema para los consumidores”.

[https://www.youtube.com/watch?v=52Fod9p-lxU&t=1001s](https://www.youtube.com/watch?v=52Fod9p-lxU&t=1001s)

## TOPOLOGÍAS DE RED

### LAN to LAN

Cuando hablamos de un servicio de "Internet LAN to LAN" (Red de Área Local a Red de Área Local), nos referimos generalmente a una solución que permite interconectar dos o más redes locales (LANs) que están en ubicaciones geográficas distintas, para que puedan compartir recursos e información de forma segura como si estuvieran en la misma red física.

Una de las formas más comunes y accesibles de implementar esto, especialmente para pequeñas y medianas empresas (PyMEs) o para conectar sucursales con necesidades moderadas, es a través de una VPN de Sitio a Sitio (Site-to-Site VPN) utilizando las conexiones a Internet existentes en cada lugar.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image023.jpg)

### WPLS

Un caso de uso muy común y claro para un servicio de Internet de tipo MPLS (Multiprotocol Label Switching) es el de una empresa con múltiples sucursales que necesita una red privada, segura y de alto rendimiento para interconectarlas y garantizar la calidad de sus aplicaciones críticas.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image025.jpg)

PEERING

### SERVICIO CDN

Plataforma distribuida para entrega de contenido. Sirve contenido más cerca de los usuarios. Mejora el desempeño de los servicios a los usuarios. Menor costo para el proveedor de contenido y el ISP

#### CDNs Tradicionales y Telco

• Akamai

• Cloudflare

• Level3

• Limelight Networks

#### Content Provider own-CDNs

• Google

• Netflix

• Facebook

### Puntos de Intercambio de tráfico: IXPs

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image027.jpg)

Un IXP es un sitio donde los proveedores de Internet se interconectan. Infraestructura compartida entre diferentes operadores para intercambiar su tráfico. Normalmente habrá varios AS que se interconectan, lo que lo distingue de un peering privado que se hace entre dos redes.

Un IXP permite interconectar redes que son organizaciones separadas: sistemas autónomos independientes.

Generalmente ubicados en un lugar neutral, Universidades, datacenters neutrales, organizaciones sin fines de lucro.  La Organización esta generalmente administrada por asociaciones sin fines de lucro, formadas por los operadores. Algunas veces el gobierno los aloja y promueve

#### Algunas ventajas de los IXPs (estabilidad y resiliencia)

1-       Menor latencia para las aplicaciones

2-       Tráfico local se rutea localmente

3-       Menores costos

4-       Posibilidad de CDNs

5-       El tráfico de una región/pais/zona no es visto desde otras regiones/paises

6-       Introduccion de nuevas tecnologias (IPv6, RPKI, etc)

7-       Sentido de "comunidad”

8-       Compartir problemas, estrategias, acciones en común

#### Tipos de IXP

• Modelo de Capa 3

• Modelo de solo Capa 2

• Modelo de Capa 2 + Route Server

#### Ventajas de usar un Route Server

• Escalabilidad de ruteo

• Separación de ruteo y reenvío de paquetes (forwarding)

• Simplifica la administración de configuración de ruteo en los ISPs

• Evita el envío de información falsa de ruteo

• Buena ingeniería de ruteo

#### Ejemplos de route-servers

• arouteserver: http://arouteserver.readthedocs.io

• Herramienta en Python para generar configuración para route

servers

• Produce configuraciones para BIRD y OpenBGPd

• Soporta IRR, RPKI, WHOIS

• Soporta PeeringDB para obtener los AS-SETs

• Simple de integrar con otros sistemas

• IXP manager: https://www.ixpmanager.org

• Es un Sistema de administración completo para IX

• Incluye un portal para administración del IXP y para los

miembros

• Produce configuraciones para BIRD

#### PeeringDB

www.peeeringdb.com

• Base de datos con información para establecer

peering

• ConMene información de contacto y siMos de

peering para:

• Redes

• Puntos de intercambio

• ÚMl para que los demás puedan establecer peering

con nosotros