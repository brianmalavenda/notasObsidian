tipo::
tags::
fecha:: 2026-01-26
links::
links_externos::

## Que es blockchain?

La blockchain (como Ethereum, Polygon, Solana, etc.) es un estado global que todos los nodos validan y almacenan. [Los contratos inteligentes] tienen su propio **espacio de almacenamiento persistente**. Vive en **miles de nodos independientes** distribuidos por todo el mundo, cada uno ejecutando el software cliente (como Geth para Ethereum). Allí se guardan las variables de estado que definen la lógica de la aplicación.

Estos nodos pueden ser:

- **Nodos de Minería/Validación** (ahora con Proof-of-Stake): Los que proponen y validan bloques a cambio de recompensas. Requieren staking de 32 ETH.
- **Nodos Completos (Full Nodes):** Cualquier persona o entidad que descarga y verifica **toda la historia de la blockchain** (actualmente ~1.5 TB para Ethereum). Estos nodos validan transacciones y bloques independientemente.
- **Nodos de Archivo (Archive Nodes):** Como los completos, pero guardan **todo el estado histórico en cada bloque** (~15 TB), esenciales para servicios como exploradores de bloques.
- **Nodos Livianos (Light Nodes):** Solo descargan los headers de los bloques, confían en los nodos completos para los datos.


En Web3 la identidad y el estado están ligados a la [billetera (wallet)] del usuario y a la blockchain. Es decir que el método de autenticacion se realiza sin el clásico usuario/contraseña sino al hacer clic en "Conectar Wallet", la dApp solicita a tu wallet que [firme un mensaje criptográfico]. Esa firma prueba que eres el dueño de esa dirección pública sin revelar tu clave privada. No hay contraseña ni servidor que valide. La blockchain y tu firma son la prueba.
Todo se guarda en la blockchain, esto es costoso porque se paga por almacenamiento y es público.  Por eso los datos "criticos" se guardan en el almacenamiento del contrato inteligente, asociados a tu dirección.
Para datos privados o pesados se usan protocolos como [IPFS] (InterPlanetary File System) o [Arweave] (almacenamiento permanente).
Por ejemplo, la dApp sube el archivo (ej. avatar, configuración de perfil) a IPFS, que devuelve un **hash único (CID)**. Ese hash (un string pequeño) es el que se guarda **en la blockchain**. Para leer los datos, la aplicación frontend consulta la red IPFS usando ese hash.

#### Bases de datos - trazabilidad

Indexar y consultar datos directamente de la blockchain es complejo. Servicios como **The Graph** permiten crear "subgrafos" que indexan eventos emitidos por los contratos y los ponen en una base de datos accesible por GraphQL. Es una capa de consulta descentralizada.

#### Estado de la Aplicación (Carrito de compras, Progreso no guardado)

Este es el dato más temporal. No se guarda en ningún servidor central. Se maneja en el estado del frontend (usando React, Vue, etc.) en el navegador del usuario, o en el localStorage. Solo cuando el usuario toma una acción definitiva (ej. "Comprar", "Guardar Progreso"), se ejecuta una transacción que actualiza el estado permanente en la blockchain.

### Costos

Cada línea de código de tu contrato inteligente cuesta gas para desplegar. Desplegar un contrato ERC-20 básico puede costar entre 0.02 y 0.1 ETH (dependiendo del precio del gas). Con ETH a $3,000, eso es $60 a $300 solo para poner el contrato en la red principal (Mainnet).
Si necesitas modificar lógica (con patrones de upgradeability), cada upgrade es otra transacción costosa.

Las funciones que escriben en almacenamiento (storage) son mucho más caras que las que solo leen (view/pure).
Ejemplo de costos relativos:
store(uint256 x) (escribir): ~20,000 - 100,000 gas
getValue() (leer): 0 gas (gratis, solo el nodo paga CPU)

Para desarrollo, usas testnets (redes de prueba):
Sepolia, Goerli, Holesky (Ethereum testnets)
Polygon Mumbai (para Polygon)
Aquí obtienes ETH de prueba (faucet) gratuitamente de:
https://faucet.quicknode.com/ethereum/sepolia
https://faucet.polygon.technology/

Costos para los Usuarios de tu dApp:
1. Interacción con tu Contrato:
Cada vez que un usuario ejecuta una función que modifica estado, paga gas.

El desarrollador define cuánto gas consume cada función mediante la optimización del código.

Ejemplos en tu dApp:
1. "Mintear un NFT": Usuario paga gas
2. "Transferir un token": Usuario paga gas
3. "Votar en un DAO": Usuario paga gas
Solo la lectura es gratuita para el usuario

**Soluciones comunes:**
Meta-transacciones/Relayers: Tú (el desarrollador) pagas el gas por los usuarios, luego recuperas el costo de otras maneras (subscriptiones, tokens).
Sistemas de Lotes (Batching): Agrupar muchas acciones en una sola transacción.

Usar Layer 2: Donde el gas es 10-100x más barato:
Optimistic Rollups (Arbitrum, Optimism)
ZK-Rollups (zkSync, Polygon zkEVM)
Sidechains (Polygon POS)

Patrón "Commit-Reveal": El usuario envía una transacción barata ahora y revela los datos más tarde.

Ejemplo de Cálculo de Gas Actual:
solidity
// Función simple que guarda un número
function setNumber(uint256 _newNumber) public {
    number = _newNumber; // Esto cuesta ~20,000 gas
}
Si el precio del gas es 20 gwei (0.000000020 ETH)

Costo = 20,000 × 0.000000020 = 0.0004 ETH

Con ETH a $3,000 = $1.20 por ejecutar esta función

Moraleja: En Mainnet, cada storage.write es costoso. Por eso las dApps optimizan al máximo y buscan alternativas (Layer 2, almacenamiento off-chain con hashes on-chain).


### **Guía para Introducirte en el Desarrollo Blockchain**

Como experto, te recomiendo esta ruta de aprendizaje:

1. **Fundamentos Criptográficos (Base):**
    
    - Entiende los conceptos de **hash**, clave pública/privada, firma digital. No hace falta ser matemático, pero sí comprender para qué sirven.
        
2. **Blockchain 101:**
    
    - Aprende cómo funciona una blockchain a alto nivel: bloques, transacciones, consenso (PoW, PoS), nodos, inmutabilidad.
        
3. **Elige una Plataforma y su Lenguaje (Enfoque):**
    
    - **Ethereum Virtual Machine (EVM) es el estándar industrial.** Te abre las puertas a Ethereum, Polygon, Avalanche, BSC.
        
    - **Lenguaje: Solidity.** Es el más demandado.
        
    - **Alternativas:** Solana (Rust), Bitcoin (Script, más limitado).
        
4. **Curso de Solidity y Contratos Inteligentes:**
    
    - **CryptoZombies:** Tutorial interactivo genial para empezar.
        
    - **Solidity by Example:** La documentación oficial.
        
    - Aprende la estructura de un contrato, variables de estado, funciones, modificadores, eventos, manejo de tokens (ERC-20, ERC-721).
        
5. **Herramientas de Desarrollo (Tu Kit):**
    
    - **Entorno:** Remix IDE (en el navegador, perfecto para empezar).
        
    - **Testing y Deployment Frameworks:** Hardhat o Foundry (este último es más avanzado pero muy poderoso). Aprende a escribir tests (¡es crucial!).
        
    - **Redes de Prueba:** Usa Sepolia o Goerli (testnets de Ethereum) para desplegar gratis.
        
6. **Construye tu Primera dApp Completa (Frontend + Backend Blockchain):**
    
    - Un **To-Do List** que guarde las tareas en un contrato.
        
    - Un **Token ERC-20** simple.
        
    - Un **Marketplace simple de NFTs** (esto integra casi todo: contratos, IPFS, frontend).
        
    - Para el frontend, usa **wagmi.sh** o **ethers.js/web3.js** para conectar la wallet y interactuar con el contrato.
        
7. **Patrones y Seguridad Avanzada:**
    
    - Estudia los **hacks históricos** (The DAO, overflow/underflow). Lee sobre "reentrancy", "oracle manipulation".
        
    - Aprende patrones como Upgradeable Contracts (usando Proxies), Gas Optimization.
        
8. **Sumérgete en el Ecosistema:**
    
    - Usa **The Graph** para indexar datos.
        
    - Prueba **Layer 2** como Arbitrum o Optimism.
        
    - Explora **Cross-chain** (Puentes).


---
#### Referencias

