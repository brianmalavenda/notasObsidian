tipo::
tags::
fecha:: 2025-10-23
links::
links_externos::

##  **Detectar y cazar con NDR**

A diferencia de la web pública, la web oscura se basa en herramientas de anonimización como el navegador Tor, Invisible Internet Project (I2P) y las redes peer-to-peer (P2P) de Freenet que oscurecen los orígenes de los usuarios, cifran el tráfico y evaden la detección. A pesar de su capacidad para ofuscar la actividad, los signos del movimiento de la red oscura todavía se capturan en los datos de la red.

Las señales reveladoras incluyen el uso inusual del puerto, patrones de tráfico cifrados y comunicación con los nodos de entrada o salida de Tor (explorados más a fondo en el paso 4).

Los sistemas NDR monitorean el tráfico de la red en tiempo real, aprovechando la IA, el aprendizaje automático y el análisis de comportamiento para identificar actividades sospechosas o maliciosas. NDR también mantiene registros completos de la actividad de la red, proporcionando historia y contexto esenciales. Con estos conocimientos, los equipos pueden integrar NDR en su infraestructura y procesos SOC para mejorar el tiempo medio de detección (MTTD) y respuesta (MTTR) a las amenazas cibernéticas, incluidas las planteadas por la web oscura.

Supervise el tráfico en su red principal, entornos perimetrales y segmentos internos. Las recomendaciones clave incluyen:

- **Coloque los sensores NDR en ubicaciones estratégicas:**  
    Centrarse en segmentos de red que albergan activos de alto valor para detectar actividad de comando y control (C2) e intentos de exfiltración de datos.
    
- **Analizar el tráfico norte-sur:** Utilice NDR para inspeccionar las comunicaciones internas a externas y detectar posibles interacciones en la web oscura.

### Comience con la línea base de la red

Las implementaciones de NDR a menudo comienzan con un período de referencia de red de 30 días que permite que la plataforma conozca el tráfico normal de su organización. Una vez completado, NDR puede marcar automáticamente indicadores de actividad de la web oscura, incluidos:

- Nuevas comunicaciones con IP externas previamente desconocidas
    
- Conexiones excesivas entre pares
    
- Protocolos sospechosos de transferencia de archivos o tráfico a dominios inusuales
    
- Tráfico saliente inusual disfrazado de normal, lo que indica una posible exfiltración de datos a los mercados de la dark web
    

Una vez establecida la línea base, ahora puede refinar la configuración NDR para automatizar la detección de indicadores específicos de la web oscura.

Tenga en cuenta que si su red ya está comprometida, debe tener cuidado de no permitir que el NDR perciba la actividad de amenaza como “normal”

Es por esto que basar la red requiere un análisis activo y una comprensión de su entorno. 

### **Automatizar la detección de la actividad de Tor**

- Configure alertas dinámicas para dispositivos que se comunican a través de puertos Tor predeterminados (9001, 9030, 9050).
    
- Supervise los registros del túnel para detectar patrones irregulares como encabezados comprimidos de seguridad de la capa de transporte (TLS), comportamientos de negociación únicos, sesiones inusualmente largas y un alto uso de ancho de banda.
    
- Busque firmas de tráfico Tor, incluidas longitudes de paquetes distintivas y apretones de manos.
    
- Realice un seguimiento de las conexiones a nodos de entrada Tor, relés, puentes y nodos Obfourscator o “obfs4” conocidos. Marque el tráfico que cambia con frecuencia entre múltiples IP externas o interactúa con servicios de anonimización.
    
- [Plataforma NDR abierta de Corelight](https://corelight.com/products/open-ndr/?utm_source=bleepingcomputer&utm_medium=article-1&utm_campaign=awareness-wave-2) con [Investigador](https://corelight.com/products/investigator?utm_source=bleepingcomputer&utm_medium=article-1&utm_campaign=awareness-wave-2) puede proporcionar visibilidad de las conexiones Tor a través de metadatos de red (incluidas conexiones, protocolos y conexiones y certificados TLS), firmas Suricata y detecciones de algoritmos de aprendizaje automático.

### Monitor para conexiones I2P y P2P

- Establezca alertas dinámicas para el tráfico en los puertos I2P (7650–7659) y los puertos BitTorrent/P2P (6881–6889).
    
- Supervise el alto tráfico UDP saliente hacia IP aleatorias o externas, señalando túneles I2P.
    
- Esté atento a picos periódicos en IP desconocidas o no resueltas y puertos UDP oscuros que son comunes con el descubrimiento de pares I2P.
    
- Detectar sesiones P2P persistentes y de larga duración en direcciones IP distribuidas, lo que indica actividad de Freenet.
    
- Busque certificados autofirmados típicos de Freenet y otras herramientas de anonimización.
    
- Marque estaciones de trabajo y dispositivos (incluido IoT) que muestren conexiones persistentes a nombres de dominio aleatorios o de alta entropía, un signo común de los servicios de anonimización.
    
- La luz central [Recopilación de tráfico cifrado](https://corelight.com/products/analytics/encrypted-traffic?utm_source=bleepingcomputer&utm_medium=article-1&utm_campaign=awareness-wave-2) Puede ayudar a identificar certificados inusuales, cifrado inesperado y otras anomalías TLS que podrían indicar el uso de conexiones I2P y P2P cifradas.
    

### Realice un seguimiento de la actividad DNS sospechosa

- Supervise los registros de DNS en busca de consultas a direcciones .onion, subdominios poco comunes e intentos de acceder a dominios asociados con herramientas de anonimización.
    
- Marque consultas a dominios de baja reputación, raramente utilizados o maliciosos, especialmente aquellos vinculados a VPN o proxies. Por ejemplo, las solicitudes DNS que involucran el dominio .su, reservado para la ex Unión Soviética, casi nunca son legítimas.
    
- Detecta dispositivos que evitan el DNS interno y en su lugar utilizan servidores DNS externos. Esto podría indicar el uso de herramientas de anonimización, pero también es probable que sea una violación de las políticas y preferencias de seguridad de la red de la organización.
    

### Monitorizar conexiones VPN

- Detectar conexiones a proveedores de VPN de consumo conocidos (por ejemplo, NordVPN, ExpressVPN, ProtonVPN).
    
- Alerta sobre puertos VPN no estándar (OpenVPN: 1194, Protocolo de transporte de capa dos o L2TP: 1701).
    
- Marque el tráfico enrutado a través de OpenVPN, IPSec o WireGuard, incluido el uso de certificados SSL/TLS personalizados vinculados a estos servicios, para determinar si están permitidos por las políticas y prácticas actuales.
    
- El paquete VPN Insights de Corelight identifica más de 400 protocolos, proveedores y tipos de VPN únicos y los registra junto con metadatos críticos (como el país de origen) para su investigación.
    

### Monitorizar “viajes imposibles,” geolocalización y anomalías similares

- Identifique instancias de “viaje imposible” utilizando datos de geolocalización IP de usuarios o dispositivos (por ejemplo, inicios de sesión desde países distantes cuando los usuarios se encuentran en sus oficinas).
    
- Detecte conexiones de regiones o países sospechosos fuera del alcance operativo normal de su organización.
    
- Busque tráfico sin ninguna aplicación comercial legítima identificable
    

### Movimiento lateral de la bandera que señala posible infiltración

- Marque el tráfico interno que salta entre múltiples sistemas antes de llegar a puntos finales externos.
    
- Supervise la actividad inusual entre dispositivos internos, especialmente utilizando protocolos inesperados como servidores proxy/túneles SOCKS.
    
- La colección de tráfico cifrado de Corelight también puede identificar tráfico de administración remota inusual, incluso en conexiones cifradas, lo que ayuda a ver un uso potencialmente malicioso de SSH y RDP a medida que los atacantes se mueven lateralmente dentro de una red.
    

### Detectar malware y balizamiento de comando y control (C2)

- Uso [Yara](https://corelight.com/products/yara?utm_source=bleepingcomputer&utm_medium=article-1&utm_campaign=awareness-wave-2) para analizar archivos extraídos del tráfico de red. Busque malware o binarios sospechosos.
    
- Verifique los registros en busca de patrones de actividad “check-in”. Estos pueden ocurrir a intervalos regulares, como cada 5 minutos o cada hora.
    
- De Corelight [Colección C2](https://corelight.com/products/analytics/command-and-control?utm_source=bleepingcomputer&utm_medium=article-1&utm_campaign=awareness-wave-2) identifica docenas de marcos de ataque, RAT y malware que a menudo se utilizan para organizar ataques, establecer C2 y descargar herramientas adicionales para la expansión de ataques.
    

### Añadir inteligencia sobre amenazas

- Correlacione la actividad conocida de la web oscura agregando fuentes de inteligencia sobre amenazas. Integre feeds para marcar indicadores de compromiso (IOC) como hashes, IP y dominios C2.
    
- Considere contratar un servicio de inteligencia de amenazas de terceros para monitorear la web oscura en busca de conversaciones sobre su organización o filtraciones de datos de su entorno.
    
- Realice un seguimiento de los intentos de inicio de sesión desde ubicaciones sospechosas o comprometidas con monitoreo de credenciales externo.
    
- Intel Framework de Corelight combina millones de indicadores a velocidad de línea, generando alertas y registros para detección e investigación puntuales.
    

Una solución NDR bien ajustada mejora significativamente la capacidad de su organización para detectar la actividad de la web oscura y refuerza la postura general de ciberseguridad.

La plataforma Open NDR de Corelight cuenta con detección multicapa integrada, análisis de archivos, monitoreo avanzado de protocolos y recopilación integral de metadatos de red a largo plazo.  
   
**Para descubrir más sobre Corelight NDR o discutir cómo activar estas capacidades de detección de la web oscura dentro de su propia red, [Visita corelight.com](http://corelight.com/?utm_source=bleepingcomputer&utm_medium=article-1&utm_campaign=awareness-wave-2).**

---
#### Referencias

