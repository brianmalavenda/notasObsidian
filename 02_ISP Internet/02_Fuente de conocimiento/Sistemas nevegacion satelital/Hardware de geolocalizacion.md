tipo:: #internet
tags:: #sistema-global-navegacion #chip 
fecha:: 2025-09-04
links:: 
links_externos::


### Chipset especializado

El chip **AT6558** (mencionado en los resultados de búsqueda) es un chip multi-GNSS que permite recibir señales de BDS, GPS, GLONASS, Galileo, entre otros [](https://www.dfrobot.com/product-2080.html?srsltid=AfmBOorZ9sYmCIYvS6Oed4T0Ymbq-NABf70VEgHxX706QCdb6zKaVBfR). Este chip tiene canales de seguimiento dedicados para procesar señales satelitales.  El chip GNSS (como el AT6558) puede seguir recibiendo señales de los satélites para mantener su sincronización y preparación para un posicionamiento rápido (por ejemplo, en modo "standby" o de bajo consumo). Esto se debe a que el chip necesita periodically actualizar su almanaque y efemérides satelitales para reducir el tiempo de adquisición (TTFF) cuando se reactiva la geolocalización

#### SKU:TEL0132

Se trata de un módulo de posicionamiento y navegación de pequeño tamaño basado en el AT6558 (un auténtico seis en un chip multimodo de navegación y posicionamiento por satélite). Puede proporcionar información de ubicación en tiempo real y admitir una variedad de sistemas de navegación por satélite, incluidos 32 canales de seguimiento. Además, el módulo puede recibir señales GNSS de 6 sistemas de navegación por satélite simultáneamente, que incluyen el BDS (sistema de navegación por satélite Beidou) de China, el GPS estadounidense, el GLONASS ruso, el GALILEO europeo, el QZSS de Japón y el sistema de aumento de satélite SBAS (WAAS, EGNOS, GAGAN MSAS), y realizar el posicionamiento, la navegación y la sincronización conjuntos.

![[Pasted image 20250904173938.png]]

En el modo de salida en serie, el módulo es compatible con varias placas base equipadas con salida en serie: Arduino, Raspberry Pi, STM32, etc. El error de precisión de posicionamiento se mide en unos 3 m, básicamente el mismo que el de los teléfonos inteligentes. El consumo de energía del módulo es tan bajo como 0,1 W y puede funcionar de forma continua durante mucho tiempo con una fuente de alimentación pequeña.

https://www.dfrobot.com/product-2080.html
https://wiki.dfrobot.com/GPS_%2B_BDS_BeiDou_Dual_Module_SKU_TEL0132

### Antena específica

Los dispositivos requieren una antena optimizada para las frecuencias de los satélites (p. ej., BDS opera en bandas como B1I a 1561.098 MHz, B2I a 1207.140 MHz) . Esta antena es crucial para captar señales débiles desde el espacio.

### Adicional

Algunos dispositivos, especialmente aquellos con capacidades de mensajería satelital (como el servicio de mensajes cortos de BDS), pueden incluir componentes adicionales para procesar y transmitir señales, no solo recibirlas 

Estos elementos físicos son independientes del software y permiten al dispositivo interactuar con los satélites incluso si el usuario no está utilizando activamente servicios de geolocalización. Cuando desactivas la "geolocalización" en la configuración de software de tu dispositivo (p. ej., en Android/iOS), generalmente se detiene el acceso de las aplicaciones a los datos de ubicación. Sin embargo, esto no necesariamente desactiva completamente el hardware de navegación satelital.

---
#### Referencias

