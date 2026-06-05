tipo::
tags::
fecha:: 2026-04-14
links::
links_externos::

### PASO 1: Preparación del Router TP-Link ER605 (Fuera de línea)

No lo conectes todavía al Huawei. Conéctalo solo a tu PC de administración.

1. **Crear las interfaces VLAN:** Entra a la gestión del ER605 y crea cada una de las VLANs (10, 20, 30, 40, 50, 60, 70).
    
2. **Configurar el DHCP para cada VLAN:** Asigna los rangos de IP a cada una (ej. VLAN 10: `192.168.10.1/24`, etc.). Esto es vital para que, cuando conectes algo, reciba IP automáticamente.
    
3. **Configurar el puerto Trunk:** Define que el puerto **1-2** del ER605 sea de tipo **Trunk** (o "Tagged") para todas esas VLANs.
    
4. **Pre-configurar la WAN:** * Carga el usuario y contraseña PPPoE que anotaste.
    
    - **Crucial:** En la configuración de la WAN, activa la opción de VLAN y ponle el **ID 10** (que es el que vimos en tu captura del Huawei). Sin esto, no tendrás internet al puentear.

| **Campo**               | **Valor**                                             |
| ----------------------- | ----------------------------------------------------- |
| **Connection Type**     | **PPPoE**                                             |
| **Username / Password** | Tus datos de acceso (ej: `usuario@telecom` o similar) |
| **MTU**                 | **1492**                                              |
| **VLAN Enable**         | **Marcado (Tildado)**                                 |
| **VLAN ID**             | **10**                                                |
| **Primary DNS**         | **1.1.1.1**                                           |

### PASO 2: Configuración del Switch Keeplink

Ahora, conecta el puerto **1-2** del ER605 al puerto **2-1** de tu switch.

1. **Configurar el puerto 2-1 como Trunk:** Debe aceptar todas las VLANs con etiquetas (Tagged).
    
2. **Asignar los puertos de Acceso:** Aquí es donde aplicas tu mapa físico:
    
Para que tu esquema de **máximo aislamiento** funcione, el switch debería verse así (asumiendo que usamos los puertos que definimos en el mapa físico anterior):

| **VLAN Name** | **ID** | **Member Ports** | **Tagged Ports** | **Untagged Ports** | **Notas**                          |
| ------------- | ------ | ---------------- | ---------------- | ------------------ | ---------------------------------- |
| **ADMIN**     | 10     | 1, 2             | 1                | **2**              | Puerto 2 para tu PC Admin          |
| **DMZ**       | 20     | 1                | 1                | -                  | (Solo virtual dentro de Proxmox)   |
| **PROD**      | 30     | 1, 3             | 1                | **3**              | Puerto 3 para Servidor Prod (A)    |
| **BACKUP**    | 40     | 1                | 1                | -                  | (Solo virtual dentro de Proxmox)   |
| **SANDBOX**   | 50     | 1, 4             | 1                | **4**              | Puerto 4 para Servidor Sandbox (B) |
| **HOME**      | 210    | 1, 5             | 1                | **5**              | Puerto 5 para Sagemcom HOME        |
| **INVITADOS** | 200    | 1, 6             | 1                |                    |                                    |

### El paso final: El PVID (Muy importante)

En los switches Keeplink y similares, no alcanza con poner el puerto como `Untagged`. Tienes que ir a otra pestaña del menú (usualmente llamada **VLAN Port Config** o **PVID Settings**) y asignar el ID de la VLAN a cada puerto físico:

- **Puerto 2:** PVID 10
    
- **Puerto 3:** PVID 30
    
- **Puerto 4:** PVID 50
    
- **Puerto 5:** PVID 60
    
- **Puerto 6:** PVID 70
    

**¿Por qué es necesario?** El PVID le dice al switch: _"Cuando entre un cable 'común' (como el de tu PC) por el puerto 2, ponele automáticamente la etiqueta 10 para que el router sepa que es el administrador"_. Sin el PVID, el tráfico de tus PCs no sabrá a qué red pertenece.

### PASO 3: Preparación de los Access Points (Sagemcom)

Configura los dos Sagemcom conectándolos directamente a tu PC, uno por uno:

1. Ponles una IP fija dentro de su VLAN correspondiente (ej: el Home en la `192.168.60.2`).
    
2. **Desactiva el DHCP server.**
    
3. Configura los nombres de Wi-Fi y claves.
    
4. Déjalos listos pero desconectados.
    



### PASO 4: El Momento del Cambio (Downtime mínimo)

Ahora que tu red interna es una "maquinaria" lista para girar, hacemos el puente:

1. Entra al **Huawei (Dispositivo 3)**.
    
2. Cambia el modo de la WAN de `Route` a **`Bridge`**. Asegúrate de marcar en las "Binding Options" el puerto LAN donde vas a conectar el ER605.
    
3. Desactiva el Wi-Fi del Huawei para evitar interferencias.
    
4. **Conexión física final:** Conecta un cable desde el puerto LAN elegido del Huawei al puerto **1-1 (WAN)** del ER605.
    

### PASO 5: Verificación

Si todo sale bien, en menos de 2 minutos el ER605 debería marcar la conexión PPPoE y verás el estado "Connected" en su panel. A partir de ahí, todos tus dispositivos conectados al switch deberían empezar a recibir sus IPs segmentadas y tener salida a internet.



### Análisis de tu configuración actual:

- **ADMIN (VLAN 10):** Tienes `Member: 1`, `Tagged: 1`. Esto está perfecto para el enlace con el router, pero **te falta un puerto "Untagged"** para conectar tu PC Admin. Si no pones un puerto (por ejemplo, el 2) como Untagged, tu PC no recibirá IP.
    
- **PROD (VLAN 30):** Tienes `Member: 1, 5`, `Tagged: 1`, `Untagged: 5`. **¡Esto está perfecto!** El puerto 1 recibe la etiqueta del router y el puerto 5 se la entrega "limpia" al servidor de producción.
- 

---
#### Referencias

