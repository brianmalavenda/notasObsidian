tipo::
tags::
fecha:: 2025-09-08
links:: [[OpenSSL]], [[Que es una VPN]], [[Protocolo TLS]]
links_externos::


## Simétrica

Misma clave en ambos lados de servidor/cliente. ==Muy rápida pero poco escalable. ¿Como intercambiamos esa única llave de manera segura?==
Si hay más de 2, ejemplo, 1 servidor + 2 clientes o se sigue sumando esto se eleva a una cantidad de **n (n-1)/ 2** llaves distintas. Es decir, ==esto es una desventaja clara==

## Asimétrica

Esta vez en vez de contar con 2 llaves en un sistema de servidor/cliente, contamos con 4. Es decir, el servidor y el cliente cuenta con una llave/clave pública y una clave privada cada uno. Este par de claves que tiene cada entidad en un principio parece más que el sistema anterior pero resuelve la desventaja de la cantidad de **n (n-1)/ 2** llaves distintas ya que la cantidad de llaves queda sintetizada en la fórmula **n * 2**. Esto nos permite escalabilidad.
Luego, el otro problema planteado en la forma simétrica (como compartir llaves de manera segura) esta resuelto, porque tenemos una llave pública que puede ser compartida por internet sin ningún problema.

Veamos como es esto: ==Para "hablar"== con la entidad servidor tengo que ==solicitarle==, una vez lo conozca, que esto se llama **"handshake"** o **client hello** que contiene un "saludo" definiendo con que versión de TLS se van a comunicar. Luego pide su ==clave pública== para poderle mandar información. Una vez tengo su llave pública, la utilizo para cifrar el mensaje que le quiero mandar. ([[ver más ....]])
Entonces, una vez que le envio desde **cliente_1** el mensaje al **servidor**, este desencripta el mensaje usando su ==clave privada==


> NOTA: El [[Protocolo TLS]] utiliza ambas formas de cifrado (simétrica y asimétrica). 

---
#### Referencias

