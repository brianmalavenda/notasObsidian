
tipo::
tags::
fecha:: 2025-09-01
links::
links_externos::


![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

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


---
#### Referencias

