tipo::
tags::
fecha:: 2026-09-08
links::
links_externos::

### Arquitectura de la Web de Noticias Científicas

```
[Usuario / Navegador] 
    │
    ├── 1. Descarga el Frontend (Servido desde IPFS/Arweave o CDN Descentralizada)
    │
    ├── 2. Consulta de Artículos (RPC Ciego / Gateway Lectura a Nodos Intermedios)
    │
    ├── 3. Carga del Documento (Lector de PDF/Viewer integrado vía P2P / WebTorrent / Helia)
    │
    └── 4. Verificación Transparente (Validación del Hash en segundo plano)
```

### 1. El Frontend Descentralizado (La Cara Visible)

En una web tradicional, el código HTML/JS vive en un servidor central (como AWS o Google Cloud). En una dApp periodística y de ciencia:

- **Alojamiento en IPFS/Arweave:** La interfaz web (React, Vue, Svelte) se compila como un sitio estático, se procesa en un paquete de archivos y se sube a IPFS. Esto significa que la propia página web no tiene un servidor central que pueda ser dado de baja o censurado.
    
- **Dominio ENS / Unstoppable Domains / IPNS:** En lugar de depender de DNS tradicionales, la web usa nombres de dominio descentralizados (ejemplo: `noticias-ciencia.eth`). Para usuarios comunes, se puede mapear un proxy inverso seguro o gateways públicas de IPFS (como Cloudflare IPFS Gateway).
    

### 2. Capa de Lectura Ciega para el Público General

El usuario común no necesita instalar un nodo ni tener una billetera cripto para leer la información.

- **Nodos Lectores / Gateways Intermedias:** Los **Nodos Intermedios** de la red exponen un endpoint de lectura público (API REST/GraphQL o JSON-RPC) en modo solo lectura.
    
- **Caché y Feed de Noticias:** Cuando se emite el evento `DocumentAdded` en la Blockchain, los nodos intermedios generan un índice de noticias (Título, Resumen, Categoría, Fecha, CID). La web consulta este índice sin requerir que el visitante se conecte directamente a la red P2P.
    

### 3. Visualización y Descarga del Documento Científico

Cuando el usuario hace clic en una noticia o investigación para leer el informe completo:

1. **Recuperación P2P en el Navegador:** Utilizando librerías de cliente P2P livianas ejecutadas directamente en el navegador (como **Helia / JS-IPFS** o **WebTorrent**), la web solicita los fragmentos del PDF/documento a la red de nodos terminales a través de WebSockets o WebRTC.
    
2. **Verificación Transparente (Integridad en Pantalla):** El código JavaScript que ejecuta el navegador calcula de forma transparente el hash SHA-256 del binario recibido y comprueba en segundo plano si coincide exactamente con el CID registrado en el Ledger.
    
3. **Insignia de Autenticidad ("Badge de Validación"):** La interfaz muestra un indicador verde que certifica:
    
    - _Estado:_ `Documento Inmutable y Verificado`
        
    - _Fecha de Certificación:_ Timestamp del bloque.
        
    - _Atestación:_ Validado por el quórum de Nodos Ledger (sin revelar la IP del investigador original).
        

### 4. Flujo de Publicación (Para Investigadores desde la Web)

Para que un científico publique una noticia o trabajo sin comprometer su identidad:

1. **Acceso Anónimo:** El científico ingresa al panel de publicación utilizando un navegador seguro (ej. Tor Browser) o autenticándose mediante **Zero-Knowledge Proofs (zk-SNARKs)** usando firmas de grupo.
    
2. **Firmado Local:** El navegador del científico procesa el archivo localmente, genera el hash SHA-256, fragmenta el PDF y lo envía cifrado hacia los nodos relé de almacenamiento.
    
3. **Envío de Transacción a Través de Relayer:** Para no pagar tarifas ni exponer su dirección IP al emitir la transacción, se utiliza un servicio de **Relayer** (Meta-transacciones / Account Abstraction ERC-4337). El Relayer paga el costo de inclusión en el Ledger sin conocer la identidad ni la IP del autor original.
    

### Resumen de Beneficios para un Portal de Noticias Científicas

- **Imposible de Tumbar / Incensurable:** El sitio web y los documentos viven en cientos de nodos. Si un servidor es apagado, la web sigue operando desde el resto de la red P2P.
    
- **Verificabilidad Periodística Cero-Confianza:** Ni los editores de la web ni un tercero pueden alterar el contenido de una investigación una vez publicada. El lector tiene certeza matemática de que lee la versión original.
    
- **Protección Total de Fuentes:** Los investigadores publican mediante abstracción de cuenta y pruebas de cero conocimiento, permitiendo certificar su condición de "investigador acreditado" sin revelar quién es ni desde dónde sube el informe.


---
#### Referencias

