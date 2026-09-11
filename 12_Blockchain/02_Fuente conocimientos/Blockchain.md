tipo::
tags::
fecha:: 2026-09-01
links::
links_externos::


La traducción directa de Blockchain es "cadena de bloques". Esto simula una cadena donde cada eslabon esta conectado a un eslabón anterior y uno siguiente. Cada bloque es la información sobre una TRANSACCION. El nombre metafórico remite a que cada bloque de información de la blockchain, tiene un valor que referencia al bloque anterior y al bloque siguiente volviendo el orden una secuencia progresiva de bloques imposible de romper. Es decir, los bloques se "enumeran" logicamente como 1, 2, 3, 4, etc, bajo la premisa de un sistema de números enteros, entonces entre 1 y 2 no hay nada. No existe en los numeros enteros el 1,5 o el 1,9999, por lo tanto la secuencia 1 y 2 con ese orden especificamente es "irrompible". 
En la blockchain el sistema de numeros enteros viene a ser el protocolo de orden de la cadena donde al asegurar el nodo anterior y siguiente de cada uno hago del orden algo imposible de alterar porque cambiaría toda la cadena.

Un bloque de información, que debe tener? Bueno, va a tener la información relevante del sistema de blockchain y la información de base que es la mínima para que la arquitectura pueda funcionar.

Entonces, para que un bloque se identifique como único entre todo tiene un HASH, que es como una huella digital y cada bloque hace referencia al anterior y al siguiente por ese HASH que se calcula de manera INTRÍNSECA del contenido del bloque.


![[Pasted image 20260901132251.png]]

Que pasa con el primero? no tiene bloque anterior, bueno ese es el llamado BLOQUE GENESIS, quien empieza la cadena y tiene un "anterior" lógico ya configurado por el inicio de la red. Que pasa con el último, no tiene siguiente?, ese nodo, siempre el último de la cadena es el que se crea con un ESFUERZO COLECTIVO de los nodos a través de distintos mecanismos para soldarlo a fuego a la cadena que ya existe. A esto se le llama MINAR un nuevo bloque.

Entonces, MINAR un bloque requiere de mineros, que son nodos que forman parte de la red. Ahora, no todos son mineros y esto se define con un criterio que habla de la esencia de lo "democrático" de la red. Ese criterio no esta separado del esfuerzo colectivo de como se valida. 
Por ejemplo, Bitcoin valida cada nuevo bloque a través de una **Prueba de Trabajo** #proofofwork . Cada minero debe cumplir con esta prueba de trabajo en donde el objetivo es crear un HASH de identificación del bloque con una restricción llamada nonce (ejemplo, 4 0 "ceros" al principio) que requiere fuerza de cómputo para que se pruebe, de manera aleatoria, la obtención del HASH que cumple la restricción. Hay otras como la prueba de "validación", [[IPoS]], usada por [[Ethereum]] 2 o [[POH]] usada principalmente por [[Solana]]

Esto ha generado una demanda de hardware donde las placas de video se han puesto de "moda" para esta tarea. El hardware especializado para esto son las ASIC. Los [[Equipos ASIC]] (Circuito Integrado Específico de Aplicación) son computadoras diseñadas exclusivamente para minar criptomonedas con la máxima eficiencia. Un equipo ASIC solo puede minar criptomonedas de un algoritmo específico (como SHA-256 para Bitcoin).

Cada bloque de blockchain tiene un tamaño determinado, por ejemplo, en Bitcoin es 1 MB. Ahora, en Ethereum esto cambia. Además de la información base de bitcoin, la DATA de un bloque de Ethereum puede ser un código de una aplicación y ejecutar una app dentro de la blockchain.

|**Problema**|**Solución Teórica**|**Solución en Ethereum**|
|---|---|---|
|Necesidad de una moneda que represente valor|Crear una moneda escasa que exista solo en el sistema.|Tokens "Ether".|
|Necesidad de representar a las personas y la cantidad de dinero que poseen|Cada persona que participe en el sistema debe tener un identificador único de cuenta y un saldo vinculado a dicho identificador.|Sistema de cuentas. Cualquiera puede crear libremente una cuenta con una dirección de 20 bytes vinculada a ella que identifica la cuenta.|
|Necesidad de una forma de representar las transferencias de dinero entre usuarios|Para transferir dinero se debe crear un registro de una "transacción".|Transacciones. Registran la cuenta de origen, la cuenta de destino y la cantidad a transferir.|
|Necesidad de llevar un registro de todas las transacciones|Todas las transacciones deben guardarse en alguna base de datos.|????|

Una blockchain es una clase donde entre sus propiedades tiene un array de una cadena de bloques de información, otro de transacciones pendientes, el nodo correspondiente del host y los otros nodos.

## Arquitectura

Blockchain es la tecnología, Waves, Ethereum, Bitcoin, Neo, son protocolos de comunicación de la información de una blockchain con sus propias reglas y "firmas" (estructuras de datos de los bloques).  Luego, por último tenemos la capa de Token. 

### Sideschaing

Es una cadena aparte, como una rama de un repositorio que se despega de main. Esta side chaing tiene su propio "protocolo" o reglas que indican a la subcadena como tiene que comportarse para que se cree un nuevo bloque o se valide los anteriores. Los juegos que usan pago en bitcoins en general se crean una sidechaing para que cada transferencia, pago, no tarde tanto tiempo.


---
#### Referencias

