tipo::
tags:: #internet #red/servidor_dhcp #ip/estática #red/chaddr #red/dhcpoffer #red/servidor_wins #red/mascara_de_subred #red/gateway #red/lease_time
fecha:: 2025-09-04
links::
links_externos::
## Servidor DHCP

El servidor DHCP es el ISP proveedor del servicio de internet por el cual me comunico a través del router. La definición de la asignación de IP fija o dinámica se hace en el router entonces? .. **ver**

## Servidor WINS 
Un servidor WINS (Windows Internet Name Service) con IPv4 ==es un servidor que se utiliza para resolver nombres **NetBIOS** a direcciones IP en redes Windows==, especialmente en entornos más antiguos. WINS facilita la comunicación entre dispositivos en una red mapeando nombres NetBIOS a direcciones IPv4, lo que permite a los usuarios acceder a recursos de red por nombre en lugar de por dirección IP. 

## IP Estática

Por qué una IP estática?  **cuando prima la seguridad del sistema**. En esas situaciones, que se suelen dar en redes en las que hay información confidencial, lo ideal nuevamente es optar por asignar direcciones estáticas. Con esto se consigue que no puedan darse acceso no autorizados de terceros al obtener una IP válida.
DHCP es un protocolo no autenticado. 
**DHCP Spoofing**. Que realmente se refiere a hacer funcionar servidores DHCP no autorizados en una red concreta. 

### CHADDR

CHADDR significa =="Client Hardware Address" (Dirección de Hardware del Cliente)==. Es un campo en los mensajes DHCP que contiene la dirección MAC (Media Access Control) del dispositivo cliente que está solicitando una dirección IP

## DHCPOFFER

es un ==mensaje que envía un servidor DHCP a un cliente DHCP para ofrecerle una dirección IP y otros parámetros de red, como la máscara de subred, la puerta de enlace predeterminada y los servidores DNS==.

## MASCARA DE SUBRED

es un número de 32 bits (en IPv4) que divide una dirección IP en dos partes: la parte de red y la parte de host. Imagina una dirección IP 192.168.1.10 y una máscara de subred 255.255.255.0. La máscara de subred indica que los primeros tres octetos (192.168.1) pertenecen a la red, mientras que el último octeto (10) identifica el host dentro de esa red.

## GATEWAY/PUERTA DE ENLACE

Una puerta de enlace, también conocida como gateway, es un ==dispositivo o software que permite la comunicación entre redes que utilizan diferentes protocolos o formatos de datos==. Actúa como un punto de entrada y salida, traduciendo los datos para que puedan ser entendidos por diferentes redes. En esencia, facilita la interconexión entre redes distintas, como una red local y la internet.

## LEASE TIME/TIEMPO DE CONSESION
El tiempo de concesión (lease time) en redes IPv4 es la ==duración durante la cual un servidor DHCP asigna una dirección IP a un dispositivo==. Este valor determina cuánto tiempo un dispositivo puede usar esa dirección IP antes de tener que renovar la concesión con el servidor.



---
#### Referencias
