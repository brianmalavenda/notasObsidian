tipo::
tags::
fecha:: 2025-09-08
links:: [[HASH]],  [[Firma Digital]].
links_externos::


El protocolo TLS utiliza una combinación de las claves simétricas, asimétricas, [[HASH]] y [[Firma Digital]].

## CIFRADO ASIMÉTRICO
### Client Hello

Un cliente, para comunicarse con el servidor hace un **client hello** deifniendo como va a ser la comunicación. Le dice, "vamos a usar esta version de TLS", "nos vamos a comunicar de manera asíncrona con el algoritmo....", puede ser por ejemplo RSA. Para criptografía simetrica, por ejemplo AES 256. Por último se define la firma digital, que puede ser cualquier algoritmo asimétrica, El Gamal o mismo el RSA. Para intercambiar llaves, "**key eschange**", tambien un ==algoritmo asimétrico==. Para HASH, dar integridad al mensaje, se puede usar un algoritmo, por ejemplo **SHA256**.
Todo este mensaje con definiciones de la comunicación se conoce como CIPHER SUITE
### Server Hello

La respuesta del servidor es con las comodidades elegidas, a partir del cipher suite del cliente, como se van a comunicar y con que algoritmo. En este mismo momento, el servidor envía el **CERTIFICADO DIGITAL**. Allí hay informacion del servidor y tambien se envía la ==clave pública== de el.

## CIFRADO SIMÉTRICO

Continuando la comunicación anterior, el cliente optiene la llave pública del servidor y con ella crea una **CLAVE SIMÉTRICA**. 
Entonces, el servidor, cuando recibe un mensaje del cliente, con la CLAVE SIMÉTRICA cifrada con su clave pública, EL, que tiene su clave privada, puede descifrar ese mensaje.
Entonces, despues del **Hello** la comunicación es con ==claves simétricas==. Esta clave simetrica va a servir para descriptar el mensaje. Ese mensaje va a estar, a su vez, encriptador por un algoritmo para dar integridad al mensaje. Se puede hacer, por ejemplo, con un algoritmo HASH, por ejemplo como dijimos arriba que acordaron, SHA256, entonces se hace con ese.

Con esto se garantiza, **CONFIDENCIABILIDAD Y INTEGRIDAD**. 
La firma digital trata de garantizar el NO REPUDIO, donde se utiliza la clave privada para garantizar eso.


---
#### Referencias

https://www.youtube.com/watch?v=MVn2QYfrkLk
Herramientas: Wireshark