tipo::
tags::
fecha:: 2026-09-10
links::
links_externos:: https://docs.soliditylang.org/en/v0.8.37/


Es un código que corre en una blockchain. En programación es una clase directamente que contiene varias propiedades y puede ser instanciada infinidad de veces.

Estas son las propiedades de base que debe tener todo smartcontract. Para profundizar sobre smartcontrar hay que ver [[Solidity]]

| **Campo**   | **Descripción**                                                                                                                                              |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **balance** | Cantidad de ether que posee esta cuenta. Es el dinero que el contrato tiene. "La persona deposita el dinero en el smartcontract donde se almacena el dinero" |
| **storage** | Almacenamiento de datos para este contrato. El dato que se almacena                                                                                          |
| **code**    | Código de máquina sin procesar (_raw machine code_) para este contrato.                                                                                      |

Cuando esto se compila nos da dos resultados: el ABI y el ByteCode
ABI: es como una API para llamar a las funciones dentro de los smartcontracts. El bytecode es lo que entra a la blockchain.

Una vez que se ah deployado genera el IDE el acceso a las funciones y variables publicas del smartcontract

![[Pasted image 20260910120559.png]]

transacción creada con solidity

```json
{
  "status": "1 Transaction mined and execution completed",
  "transaction_hash": "0x8efec28d9aae552fddce4df1bbaca750e17cb314d672c399534023e3c91b58d8",
  "block_hash": "0x4d52fc880d5da7c5fbd42d2fe58ee7675aabae592278d5e0f560674fde387fd6",
  "block_number": 1,
  "contract_address": "0xd9145CCE52D386f254917e481eB44e9943F39138",
  "from": "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4", //esta es la billetera que creo el smartcontract - esto lo provee el propio IDE de desarrollo REMIX de ethereum
  "to": "Inbox.(constructor)",
  "transaction_cost": "480747 gas",
  "execution_cost": "380531 gas",
  "output": "0x608060405234801561000f575f5ffd5b506004361061003f575f3560e01c8063368b877214610043578063ce6d41de1461005f578063e21f37ce1461007d575b5f5ffd.....",
  "decoded_input": {
    "string initialMessage": "Hola mundo"
  },
  "decoded_output": "-",
  "logs": [],
  "raw_logs": []
}
```

ahora si yo consulto mi variable "message" con la funcion getMessage, la transacción es esta: 

```json
{
  "from": "0x5B38Da6a701c568545dCfcB03FcB875f56beddC4",
  "to": "Inbox.getMessage() 0xd9145CCE52D386f254917e481eB44e9943F39138",
  "execution_cost": "3328 gas (Cost only applies when called by a contract)",
  "input": "0xce6...d41de",
  "output": "0x0000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000a486f6c61206d756e646f00000000000000000000000000000000000000000000",
  "decoded_input": {},
  "decoded_output": {
    "0": "string: Hola mundo"
  },
  "logs": [],
  "raw_logs": []
}
```

como es una consulta, esta operación no se cobra.

Account 1 es la billetera digital que crea el contrato
![[Pasted image 20260917151228.png]]

RemixVM es la blockchain donde el serivcio es de terceros en este caso.


Conectar a la billetera virtual, wallet.

Una vez que tengo configurada mi billetera de Metamask para poder visualizar las redes de prueba, selecciono Sepolia. Luego voy a este link https://cloud.google.com/application/web3/faucet/ethereum/sepolia donde me deja conectar la billetera virtual e imprimirle dinero de prueba. En este caso 0.05 ETH en Sepolia ETH.

Una vez confirmada la transacción se visualiza un mensaje

![[Pasted image 20260917161208.png]]

Luego, verificamos en la billetera que en Sepolia se hayan agregador los ethers. Para eso vamos a la billetera de metamask

![[Pasted image 20260917161455.png]]

Acá mismo en donde dice "Red: Ethereum", seleccionamos Sepolia ETH y verificamos tener los 0.05 ETH. Una vez hecho eso, en REMIX conectamos desde Environment a nuestra wallet. Para eso elegimos la opción WalletConnect. Nos conectará y luego tenemos que indicarle que queremos usar la red de prueba

![[Pasted image 20260917161716.png]]

![[Pasted image 20260917161035.png]]

Al conectar la wallet cuando deployamos el smart contract nos va a abrir una ventana donde nos va a indicar si queremos confirmar la transaccion

![[Pasted image 20260917161233.png]]

Una vez que confirmamos, veremos la transaccion en curso

![[Pasted image 20260917161245.png]]

Con etherscan podemos ver de cualquier red de blockchain, que transacciones se hicieron.
https://sepolia.etherscan.io/tx/0xdb728f9ceb4cb0d0c3dbcaed83c74d7f2ccf15c29a7d19f19d00e258b09b4646
En esta direccion permite ver el smart contract de la transaccion hecha
https://repo.sourcify.dev/11155111/0x1f34CA81eCeC304d854a1228a8c89b413C575b30
https://eth-sepolia.blockscout.com/address/0x1f34CA81eCeC304d854a1228a8c89b413C575b30?tab=contract

---
#### Referencias

