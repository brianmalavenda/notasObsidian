tipo::
tags:: #sistema-global-navegacion 
fecha:: 2025-09-04
links:: [[CGNSS - Sistemas globales de navegación por satélite]], [[Autenticacion OsnMA]]
links_externos::


El Sistema de Navegación por Satélite BeiDou (BDS) es uno de los cuatro sistemas globales de navegación por satélite (GNSS) reconocidos por la ONU. El sistema de navegación por satélite chino Beidou-3, que presta servicios al mundo, utiliza una constelación híbrida de tres satélites en órbita. En comparación con otros sistemas de navegación por satélite, tiene más satélites de órbita alta y una fuerte resistencia a la oclusión. En particular, las características de rendimiento en latitudes bajas son más obvias. El sistema completo consta de 35 satélites, incluidos 5 satélites de órbita geoestacionaria (GEO), 3 satélites de órbita síncrona terrestre inclinada (IGSO) y 27 satélites de órbita terrestre de círculo medio (MEO). Los satélites GEO están ubicados a 58,75 grados, 80 grados, 110,5 grados, 140 grados y 160 grados de longitud este. La altitud orbital del satélite IGSO es de unos 36.000 kilómetros. Las tres superficies orbitales sincrónicas inclinadas están distribuidas uniformemente, con una inclinación orbital de 55 grados. Las trayectorias de los tres satélites IGSO se superponen. La longitud de la intersección es de 118 grados de longitud este y la diferencia de fase es de 120 grados. El satélite MEO orbita a una altitud de 21.500 kilómetros y los 27 satélites están distribuidos uniformemente en tres planos orbitales con una inclinación de 55 grados.

BDS utiliza múltiples frecuencias (B1I, B1C, B2a, B2b) que permiten mayor robustez frente a interferencias unintencionales o maliciosas.

 El sistema incluye capacidades de autenticación de mensajes de navegación (similar al OsnMA de Galileo), que verifica la autenticidad de las señales recibidas, protegiendo contra ataques que intentan engañar al receptor con ubicaciones falsas

El sistema de monitoreo del sistema de navegación por satélite Beidou de China está diseñado como un sistema global de monitoreo por satélite que consta de un sistema de monitoreo terrestre compuesto por varias estaciones de seguimiento terrestre distribuidas en China y una constelación de navegación con enlaces entre satélites. Éste es uno de los trucos únicos del sistema Beidou, que es que no requiere el despliegue de una estación global. El sistema Beidou-3 utiliza un conjunto de tecnologías que otros satélites de navegación no tienen: —— enlaces entre satélites. Las constelaciones de navegación con enlaces entre satélites pueden completar la misión conjunta de determinación de precisión de la órbita de los enlaces entre estrellas, tierra e satélites.

El sistema Beidou de China utiliza una señal de tres bandas para transmitir datos de navegación a los usuarios. La señal de tres frecuencias puede eliminar mejor el efecto de retardo ionosférico de alto orden, mejorar la confiabilidad del posicionamiento, mejorar las capacidades de preprocesamiento de datos y mejorar en gran medida la eficiencia de fijación de la ambigüedad. Además, si hay un problema con una señal de frecuencia, las otras dos frecuencias se pueden utilizar para el posicionamiento utilizando el método convencional, lo que mejora la confiabilidad y la capacidad antiinterferencias del posicionamiento.

El servicio de comunicación de mensajes cortos de la navegación por satélite Beidou también es una característica muy práctica y original. Durante el terremoto de Wenchuan en 2008, se convirtió en el único método de comunicación en la zona del terremoto. Esta función especial también tiene límite de capacidad, por lo que no es adecuada para la función de comunicación diaria, pero es más adecuada como comunicación de emergencia, utilizada como llamada de socorr

## Cifrado
 
BDS incluye un servicio de mensajería corta vía satélite (SMSS) que permite enviar textos de hasta 1.200 caracteres incluso sin cobertura móvil [beidou gob](http://www.beidou.gov.cn/)[huawei-mate-x6-pura-70-ultra](https://www.mobilemagazinehk.com/2025/02/cmhk-bds-huawei-mate-x6-pura-70-ultra.html). En el Huawei Mate X6, esta función se implementa como **"Mensajes北斗北斗"** [consumer.huawei](https://consumer.huawei.com/cn/phones/mate-x6/)[consumer.huawei](https://consumer.huawei.com/cn/phones/mate-x6/specs/). Los mensajes se transmiten through canales propietarios de BDS, lo que teóricamente reduce la exposición a interceptación por partes externas (como agencias de inteligencia de terceros países). Los mecanismos de autenticación de señal de BDS (contra spoofing) y el uso de canales propietarios ofrecen una capa adicional de seguridad compared a redes terrestres convencionales. Sin embargo, **no hay evidencia pública de que estos mensajes estén cifrados end-to-end** por defecto. 

Si bien BDS tiene cobertura global, su servicio de mensajería satelital (**北斗短报文**) está diseñado principalmente para usuarios en China y regiones adyacentes. Fuera de esta zona, la funcionalidad puede estar limitada o requerir acuerdos con operadores locales [](https://consumer.huawei.com/cn/phones/mate-x6/specs/)[](https://www.mobilemagazinehk.com/2025/02/cmhk-bds-huawei-mate-x6-pura-70-ultra.html).
Los mensajes BDS están restringidos a texto y coordenadas GPS (no soportan voz o datos de alta velocidad). Requiere visibilidad directa al cielo y condiciones atmosféricas favorables.

 El dispositivo debe proteger las claves de cifrado (si existen).
 El sistema operativo (HarmonyOS) debe estar libre de vulnerabilidades o backdoors.

 Busca artículos sobre "BDS security authentication" o "BeiDou encryption" en plataformas como IEEE Xplore. El search result [](https://gnss-imu.com/news/html/486.html) discute mecanismos genéricos de anti-spoofing en GNSS, aplicables parcialmente a BDS.

Es posible hackear el archivo de navegación y comunicación satelital BDS con un virus por software?
 
---
#### Referencias

**Este artículo fue publicado originalmente en la edición 9 de 2019 de "Exploración espacial" por Li Yufei**