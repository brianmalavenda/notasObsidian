tipo::
tags:: #telecomunicaciones
fecha:: 2026-06-16
links::
links_externos::

# Síntesis Técnica: Evolución de SIM y su Impacto en Privacidad/Anonimidad

## Definiciones Base

- **SIM (Subscriber Identity Module)**: Tarjeta física extraíble que almacena de forma segura la clave de identificación del suscriptor (IMSI) y algoritmos de autenticación (Ki, OPC). Opera como un **elemento seguro (SE)** con su propio sistema operativo (Java Card) y ejecuta el algoritmo de autenticación GSM/3G/4G/5G (AKA - Authentication and Key Agreement). La variante actual es NanoSIM (12.3mm x 8.8mm).
    
- **eSIM (Embedded SIM)**: Chip soldado directamente a la placa base del dispositivo (MFF2 - 6mm x 5mm). No es extraíble. Implementa el estándar GSMA SGP.22/SGP.32. Permite la descarga remota de perfiles operadores mediante **SM-DP+ (Subscription Manager - Data Preparation)**. Almacena múltiples perfiles (MNO - Mobile Network Operator) pero solo activa uno a la vez. La clave de identidad no se extrae físicamente.
    
- **iSIM (Integrated SIM)**: Evolución de la eSIM donde el **elemento seguro se integra directamente en el SoC (System-on-Chip)**, compartiendo el mismo silicio que el procesador, la GPU, la NPU y el módem [[citation:original]]. Opera dentro del **TEE (Trusted Execution Environment)** del SoC, reduciendo el área de superficie de ataque al eliminar buses de comunicación externos. Primeros implementaciones en Qualcomm Snapdragon 888/Gen 1 y Samsung Exynos.
    

---

## Análisis de Privacidad y Anonimidad

### Ventajas de eSIM/iSIM sobre SIM física

1. **Mitigación de SIM Swapping**:
    - La activación de una eSIM requiere **múltiples factores de autenticación** vinculados al dispositivo (no solo credenciales del operador).
    - El perfil está **criptográficamente vinculado al EUICC (eUICC)** del dispositivo, lo que dificulta la transferencia no autorizada del número.
        
2. **Resistencia a Ataques Físicos**:
    
    - La eSIM no es extraíble, por lo que el robo físico del dispositivo **no permite** el traslado de la identidad a otro hardware.
        
    - Reduce el riesgo de **clonación de SIM** mediante lectura directa del chip (Side-Channel Attacks en laboratorio).
        
3. **Gestión Remota y Cifrado**:
    
    - Posibilidad de **desactivación remota** inmediata del perfil desde la app del operador o la plataforma SM-DP+ en caso de compromiso.
        
    - Los perfiles se almacenan cifrados con **claves derivadas de hardware**, no exportables.
        
4. **Aislamiento de Perfiles**:
    
    - Soporte para **múltiples perfiles** en un solo dispositivo (hasta 5-10 activos). Esto permite utilizar diferentes identidades (números) en el mismo hardware con **separación lógica** entre perfiles, útil para aislamiento de roles (personal/profesional/anónimo).
        

### Desventajas y Riesgos para el Anonimato

1. **Mayor Trazabilidad**:
    
    - La activación de una eSIM requiere **identificación legal** (DNI/pasaporte) por normativa antilavado y antifraude [[citation:original]]. El anonimato total es **inexistente** en el registro del perfil.
        
    - El proceso de activación genera **logs adicionales** en la plataforma SM-DP+ que asocian el ICCID (Identificador de la SIM) con el IMEI del dispositivo y la dirección MAC del módem.
        
2. **Dependencia de Infraestructura Centralizada**:
    
    - La gestión de perfiles depende de **plataformas SM-DP+ de terceros** (operadores o proveedores como Saily de NordVPN). Esto introduce un punto centralizado de control y vigilancia [[citation:original]].
        
    - El cambio entre dispositivos (especialmente entre ecosistemas Android ↔ iOS) requiere **re-autenticación a través del servidor del operador**, lo que expone el historial de portabilidad [[citation:original]].
        
3. **iSIM y "Trackeo a Nivel de Chip"**:
    
    - Al integrarse en el SoC, la iSIM permite una **vinculación casi inextricable** entre la identidad del usuario (IMSI) y el hardware (CPU serial, efuses). Esto facilita el **fingerprinting** a nivel de silicio.
        
    - La gestión se realiza dentro del **TEE**, que es controlado por el fabricante del SoC (Qualcomm, MediaTek, Apple). Esto otorga a estos actores un **acceso privilegiado** al identificador celular, superando las protecciones del sistema operativo.
        

---

## Temas Relacionados de Interés en Comunicación Móvil

### 1. Privacidad en Capas Inferiores (RIL - Radio Interface Layer)

- El módem (baseband) opera con su propio sistema operativo en tiempo real (RTOS) y firmware propietario. Este firmware tiene acceso directo al IMSI, IMEI y datos de localización (TAU - Tracking Area Update), y opera **fuera del control del SO principal** (Android/Linux).
    
- La eSIM/iSIM no cambia esta arquitectura: el **control del módem sigue siendo opaco** para el usuario. El código abierto en el SO no garantiza transparencia en la capa de radio.
    

### 2. Open Source en la Gestión de eSIM

- **OpenEUICC/EasyEUICC**: Implementación de código abierto para la gestión de perfiles eSIM en Android. Permite operar sin las apps propietarias de los fabricantes [[citation:original]].
    
- **Desafíos Técnicos**:
    
    - Requiere integrarse como sistema-privilegiado (`/system/priv-app`) para acceder a las APIs del RIL (Radio Interface Layer).
        
    - Incompatibilidades con **listas blancas criptográficas (ARA-M)** del operador, que pueden bloquear la activación de perfiles no certificados.
        
    - Necesidad de **root/desbloqueo del bootloader**, lo que reduce la seguridad general del dispositivo.
        

### 3. Proyectos de Móviles con Hardware/Software Abierto

- **Proyecto mikroPhone**: Diseño de hardware y software completamente abierto (desde el PCB hasta el kernel).
    
- **Librem 5 / Divine D.**: Teléfonos con **kill switches físicos** para módem celular, micrófono y cámara, ejecutando sistemas Linux (PureOS) que evitan el _stack_ propietario de Android.
    
- **Limitación**: El módem (baseband) de estos dispositivos sigue siendo un componente **propietario y cerrado** (por ejemplo, Qualcomm o Broadcom), que es el verdadero responsable de la gestión de la identidad celular. El código abierto no ha penetrado en la capa de radio por la complejidad regulatoria (certificación FCC/CE) y la dependencia de los drivers propietarios.
    

### 4. Tendencias de Adopción y Regionalización

- **Cronología prevista**:
    
    - La SIM física convivirá al menos una década más con la eSIM [[citation:original]].
        
    - Los móviles **solo-eSIM** serán comunes en gama alta a finales de esta década; la adopción masiva global se proyecta entre **2037-2038** [[citation:original]].
        
    - La iSIM será la forma final, integrada en la mayoría de los SoCs de gama media/alta para 2030.
        
- **Asimetría Geográfica**:
    
    - Adopción temprana: **EE.UU., China, India, Australia**.
        
    - Rezago: **Rusia, Islandia** y regiones con infraestructura de operadores menos actualizada [[citation:original]].
        

### 5. Implicaciones para el Anonimato en Comunicaciones

- La integración de la identidad en el SoC (iSIM) **cierra la puerta** a la posibilidad de usar "SIMs desechables" físicas para el anonimato operacional, ya que no se pueden intercambiar físicamente.
    
- La única vía para mantener separación de identidades será mediante **múltiples perfiles** en una misma iSIM, pero todos quedarán vinculados al mismo hardware (IMEI/IMEISV), facilitando la correlación de identidades a nivel de red (Core Network).
    
- Para usuarios con altos requisitos de anonimato, la tendencia apunta a la necesidad de **dispositivos dedicados por identidad** (hardware separado), o al uso de terminales con módems modificables (como los proyectos con _OsmocomBB_ en teléfonos antiguos, incompatibles con eSIM/iSIM).
    

### 6. Consideraciones Regulatorias

- La activación de eSIM en la Unión Europea y España **requiere identificación física** por KYC (Know Your Customer) para portabilidades, por ley de prevención de fraude [[citation:original]].
    
- Los operadores tienen la potestad de exigir presencia en tienda para activar la eSIM, lo que elimina la posibilidad de alta completamente remota y anónima [[citation:original]].
    

---

## Resumen Técnico Final

|Característica|SIM Física|eSIM|iSIM|
|---|---|---|---|
|**Factor de forma**|Extraíble (12.3x8.8 mm)|Soldada (MFF2 6x5 mm)|Integrada en SoC|
|**Seguridad física**|Vulnerable a extracción/clonación|Resistente a extracción|Máxima (no hay bus externo)|
|**Gestión de perfiles**|Manual (cambio de tarjeta)|Remota (OTA vía SM-DP+)|Remota integrada en TEE|
|**Anonimato**|Permite cambio físico de identidad|Requiere re-autenticación remota|Vinculación hardware-identidad|
|**Control de usuario**|Total (físico)|Parcial (depende de operador)|Mínimo (depende del fabricante del SoC)|
|**Superficie de ataque**|Alta (acceso físico)|Media (gestión remota)|Baja (entorno aislado, pero centralizado)|

**Conclusión**: Desde la perspectiva de privacidad/análisis de amenazas, la evolución SIM → eSIM → iSIM representa un **trade-off** entre **seguridad física** (frente a robo/clonación) y **anonimato** (pérdida de la capacidad de ocultar o cambiar la identidad sin dejar rastro en infraestructura centralizada). El código abierto en la gestión de eSIM (OpenEUICC) permite cierto control del usuario, pero **no resuelve la opacidad del módem ni la dependencia de las plataformas SM-DP+ de los operadores**. Para anonimato estricto, la tendencia técnica es regresiva: la integración en el SoC (iSIM) consolida la identidad en el hardware, lo que favorece el _fingerprinting_ a nivel de red.


---
#### Referencias

