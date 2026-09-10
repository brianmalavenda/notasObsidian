tipo::
tags::
fecha:: 2026-09-08
links::
links_externos::

### Estrategia de Protección: "Encubrir" a los Nodos Investigadores

Para cumplir el objetivo de **proteger a quienes realizan las investigaciones**, la arquitectura debe impedir que el rol de un nodo pueda ser deducido por su dirección IP o por su patrón de tráfico.

#### 1. Separación de Identidades: Anonimato de Origen (Mixnets / Onion Routing)

- **Principio:** Un nodo validador Ledger jamás debe ver la IP real del nodo terminal que originó un documento.
    
- **Implementación:** Los nodos investigativos publican metadatos a la red a través de un esquema de enrutamiento por capas (como **TOR / I2P o Nym Mixnet**). La transacción ingresa al Ledger firmada por la clave privada del usuario, pero transportada a través de varios saltos intermedios. El Ledger valida _quién_ tiene permiso para publicar, pero no tiene forma técnica de saber _desde qué ubicación física o IP_ se emitió.
    

#### 2. Nodos Relé Ciegos (_Blind Proxy Terminals_)

- **Principio:** Introducir una capa de proxy inverso dentro de los nodos intermedios.
    
- **Implementación:** Los terminales de los investigadores nunca se conectan directamente a la red P2P global ni a los Ledger. Se conectan únicamente a un nodo relé local en su propia institución. Hacia el resto de la red, todos los terminales (tanto de consumo como de investigación) parecen actuar bajo la misma identidad del nodo intermedio institucional.
    

#### 3. Ofuscación de Patrones de Tráfico (Dummies & Cover Traffic)

- **Principio:** Ocultar cuándo se produce una investigación real mediante "ruido" de red.
    
- **Implementación:** Todos los nodos terminales (investigadores y consumidores) envían un flujo constante de paquetes encriptados de tamaño idéntico a la red P2P, contengan información real o fragmentos vacíos de prueba (_dummy traffic_). Un atacante que observe la red o un Ledger comprometido verá a todos los nodos enviando y recibiendo la misma cantidad de datos, haciendo imposible discriminar quién está subiendo una investigación real y quién solo consume datos.
    

#### 4. Delegación por Firma de Anillo (Ring Signatures / ZK-Proofs)

- **Principio:** Ocultar qué usuario específico dentro de un grupo institucional produjo el cambio.
    
- **Implementación:** Cuando un investigador sube un hallazgo, el Smart Contract no registra la firma exacta de su clave pública individual. Utiliza una **Prueba de Cero Conocimiento (zk-SNARK)** o una **Firma de Anillo**, donde la blockchain solo valida que _"El documento fue subido por UN investigador autorizado del departamento X"_, pero sin revelar _cuál_ de los 50 investigadores asignados a ese grupo fue el autor.


---
#### Referencias

