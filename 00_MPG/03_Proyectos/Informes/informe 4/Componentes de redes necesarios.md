tipo::
tags::
fecha:: 2026-06-30
links::
links_externos::

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

---
#### Referencias

