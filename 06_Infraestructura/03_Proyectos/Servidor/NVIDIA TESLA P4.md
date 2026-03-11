tipo::
tags::
fecha:: 2026-03-03
links::
links_externos::


### 2. Configuración necesaria en la BIOS

Para que la computadora arranque y "acepte" la Tesla como procesador secundario, debes entrar a la BIOS (presionando `Supr` o `F2` al encender) y buscar lo siguiente:

- **Primary Display / Initial Graphic Adapter:** Cámbialo a **iGPU** (o CPU Graphics). Esto le dice a la PC que use la salida de la placa madre para dar imagen, ignorando que hay algo en el puerto PCIe.
    
- **Above 4G Decoding:** Si tu placa tiene esta opción (está en el menú _Advanced > PCIe Configuration_ o _System Agent_), **DEBES activarla (Enabled)**. Las Tesla tienen mucha memoria de registro y sin esto, Windows no la reconocerá o dará "Error 12".
    
- **CSM (Compatibility Support Module):** Intenta **desactivarlo** (Disabled) para que la placa inicie en modo UEFI puro, que es como mejor funcionan estas tarjetas.

### 3. Consideraciones CRÍTICAS (No las ignores)

- **⚠️ El sobrecalentamiento:** La Tesla P4 es **pasiva** (no tiene ventilador). En un servidor, hay ventiladores gigantes que soplan aire a través de ella. En tu gabinete, **se va a quemar en minutos** si no le adaptas un ventilador directamente o le pones un túnel de aire (shroud) impreso en 3D.
    
- **Drivers:** No uses los drivers normales de GeForce. Debes descargar los drivers específicos de **NVIDIA Tesla/Data Center** desde la web de NVIDIA.
    
- **Modo WDDM:** Una vez instalada, es probable que debas usar la línea de comandos (`nvidia-smi`) para cambiar el modo de la tarjeta de "TCC" (cómputo puro) a "WDDM" si quieres que Windows la use para otras tareas.

---
#### Referencias

