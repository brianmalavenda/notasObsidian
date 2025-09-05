tipo::
tags::
fecha:: 2025-09-01
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

## Control del ancho de banda de los cables

En general, antes el ancho de banda era controlado por las proveedoras de internet. Pero actualmente mas del 70% del ancho de banda lo controlan los monopolios del software y telecomunicaciones: Google, Amazon, Facebook ..

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image001.png)

### Bloqueo a Cuba

Tan solo un cable conecta a Venezuela con Cuba y Jamaica (en rojo) de todos los múltiples cables que conectan a todos los países alrededor con el internet de Europa, EE.UU (de mayoría de nodos servidores) y demases países de centro américa y sur america.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

El gobiernon de EE.UU detuvo  la instalación de nuevos cables a través de su ente gubernamental FCC (Federal Communication Commision) [File No. SCL-MOD-20210928-00039]y además bloquear la red “Arcos” de un cable que redea a los países de centroamerica.

---
#### Referencias

