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

como es una consulta, esta operación no se cobra


---
#### Referencias

