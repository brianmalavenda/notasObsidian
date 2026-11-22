tipo:: #servidor/red tags:: #wiki #homelab #red fecha:: 2026-11-17 links:: [[01_Equipamiento_recursos]], [[Topología red]], [[Configuracion VLAN - GUIA]], [[Configurar ONT - Router - AccesPoint]] links_externos::

# 1B. Equipamiento de red necesario

> [!info] Alcance Complementa [[01_Equipamiento_recursos]]. Define **qué equipos de red hacen falta, con qué características y por qué**, cuántas bocas necesita el switch y qué cables y módulos usar. La **configuración** (VLAN, firewall, VPN) va en la pregunta 2. Para router y switch se da una **recomendación por clase y características**. Los ejemplos de familias son orientativos: verificá siempre la ficha técnica vigente.

**Leyenda:** ✅ medido · ⚠️ estimado/regla práctica · ❓ por medir.

---

## 1B.1 Escenario de dimensionamiento

|Dato|Valor supuesto|
|---|---|
|Superficie|**50 m² por piso × 3 pisos** = 150 m²|
|Servidores|1 principal + 1 de backup|
|Administración|1 PC administradora, **separada del servidor**|
|Acceso Wi-Fi|Personal de la oficina (decenas de dispositivos, no los 200 usuarios: ellos entran por internet)|
|Ubicación del rack|Un único punto central, con recorrido de cable a cada piso|

> Si tu superficie real es de 50 m² en total (no por piso), el cálculo baja a 1 AP y el switch puede ser más chico. La tabla 1B.5 se adapta restando bocas.

---

## 1B.2 Mapa de equipos

![[Pasted image 20261120092438.png]]

Equipos mínimos: **router, switch, 3 APs, UPS, cableado estructurado**. Todo lo demás es opcional.

---

## 1B.3 Router / firewall

### Por qué un router propio, independiente del ISP

|Motivo|Explicación|
|---|---|
|**Control**|El equipo del ISP suele tener firmware cerrado, sin VLAN ni firewall por zona, y el proveedor puede cambiarle la configuración|
|**VLAN y segmentación**|Sin un router que enrute entre VLAN con reglas, el aislamiento de la pregunta 2 no existe|
|**VPN propia**|Permite acceso remoto cifrado sin depender de terceros|
|**Visibilidad**|Logs y reglas auditables|
|**Reemplazo simple**|Si el ISP cambia el equipo, tu configuración no se pierde|

**El equipo del ISP queda en modo _bridge_ (solo ONT)** y tu router obtiene la IP pública, normalmente con PPPoE y, si el ISP lo exige, **etiqueta VLAN en el puerto WAN** (ver [[Configuracion de red]]).

### Requisitos

|Característica|Requisito|Por qué|
|---|---|---|
|**Rendimiento (NAT/firewall)**|≥ **900 Mbps reales** con firewall y reglas activas; si el plan supera 1 Gbps, chequear la cifra en la ficha|Un router que enruta a la mitad de la velocidad contratada es el cuello de botella de toda la red|
|**Puertos**|≥ 1 WAN + 4 LAN; ideal puerto **2,5 GbE o SFP+** hacia el switch|El enlace router-switch concentra todo el tráfico entre VLAN|
|**VLAN 802.1Q** e **inter-VLAN**|Obligatorio|Es la base de la segmentación|
|**Firewall con estado por VLAN**|Reglas por origen/destino/puerto, política _default deny_|Define quién habla con quién|
|**Servidor VPN**|**WireGuard** (preferido) y/u OpenVPN/IPsec, con rendimiento ≥ 100–300 Mbps|WireGuard es más liviano y rápido; el rendimiento VPN suele ser mucho menor que el de NAT|
|**DHCP por VLAN** con reservas|Obligatorio|IP fija para servidores y equipos de red|
|**DNS personalizado / DDNS**|Obligatorio|Permite apuntar a AdGuard y mantener el dominio si cambia la IP|
|**Doble WAN** (failover)|Deseable|Un segundo enlace evita quedar aislado|
|**Gestión local**|HTTPS/SSH desde la VLAN de gestión, **sin depender de la nube**|Si falla internet, seguís administrando|
|**Respaldo de configuración**|Exportar/importar|Reponer el equipo en minutos|
|**Actualizaciones de seguridad**|Fabricante con historial de parches|Un router desactualizado es la puerta de entrada|
|**Registro (syslog)**|Exportable al sistema de monitoreo|Base para la pregunta 6|

### Dos caminos

|Opción|Ventajas|Desventajas|Cuándo|
|---|---|---|---|
|**A. Router/firewall dedicado (clase pequeña empresa)**|Bajo consumo (5–15 W), simple, estable|Menos flexible, funciones limitadas al fabricante|**Recomendado para empezar**|
|**B. Router por software** (pfSense, OPNsense, OpenWrt) en un mini PC con 2–4 puertos de red|Flexibilidad total, IDS/IPS, rendimiento alto|Más mantenimiento, más consumo (10–25 W)|Si querés IDS/IPS y control fino|

> [!warning] No virtualizar el router dentro del servidor principal Si el router vive en una VM del servidor, **el servidor depende de la red para arrancar y la red depende del servidor**: una caída o un reinicio deja todo sin internet y sin forma de administrar. El router va en **hardware propio**.

**Recomendación:** opción A, con las características de la tabla. Familias que suelen cumplirlas (⚠️ orientativo, verificar): routers VPN/Omada de TP-Link (el equipo que usa el autor), MikroTik, UniFi de Ubiquiti. Para la opción B: OPNsense o pfSense sobre un mini PC con chips de red conocidos en Linux/BSD.

---

## 1B.4 Switch

### Por qué administrable (y no uno doméstico)

Un switch no administrable no distingue VLAN ni permite aislar puertos, aplicar ACL ni espejar tráfico. Para esta arquitectura es **obligatorio** un switch administrable.

### Requisitos

|Característica|Requisito|Por qué|
|---|---|---|
|**VLAN 802.1Q** (access/trunk, PVID)|Obligatorio|Es el núcleo de la segmentación ([[Configuracion VLAN - GUIA]])|
|**ACL**|Deseable|Filtros adicionales a nivel de puerto|
|**Port mirroring**|Recomendado|Capturar tráfico con Wireshark para diagnosticar ([[Port Mirroring]])|
|**DHCP snooping + inspección ARP**|Recomendado|Bloquea servidores DHCP falsos y suplantación ARP en puertos de acceso|
|**STP/RSTP**|Obligatorio|Evita bucles|
|**LACP**|Deseable|Agregar enlaces si lo necesitás|
|**Gestión segura**|HTTPS/SSH, VLAN de gestión propia, SNMP/syslog|La administración del switch **no** puede estar en la red de usuarios|
|**PoE+ (802.3at, 30 W por puerto)**|Para los APs|Evita fuentes y tomas en el techo|
|**Presupuesto PoE total**|≥ suma de los APs × 1,3 (⚠️ 3 APs × ~15 W ≈ 45 W → **≥ 60–90 W**)|Evita que se apaguen APs al conectar otros PoE|
|**Capacidad de conmutación**|_Non-blocking_ (puertos × velocidad × 2)|Evita cuellos de botella interno|
|**Uplinks**|≥ 1–2 puertos **SFP+ (10G)**|Para enlace al segundo switch, al backup o futura fibra|
|**Velocidad de puertos**|1 GbE de acceso; **2,5 GbE** en los de servidores|2,5 GbE reduce la copia de 1 TB de ~2,4 h a ~1 h|
|**Ventilación**|Sin ventilador si va en zona habitada|Menos ruido (el sin ventilador limita el PoE)|

### Topología

|Opción|Cuándo conviene|
|---|---|
|**Un solo switch core de 24 puertos con PoE** (recomendado)|Cada AP se conecta con un cable directo desde el rack (≤ 90 m)|
|**Core + un switch chico (8 puertos PoE) por piso**|Si hay muchos equipos por piso o hay que reducir cables que suben|
|**Dos switches core**|Si se necesita redundancia (más complejo)|

**Recomendación para el escenario:** **1 switch administrable de 24 puertos**, de los cuales **≥ 8 PoE+** y **≥ 6 de 2,5 GbE**, con **2 SFP+**. Si tu estructura crece, agregás un switch por piso.

---

## 1B.5 Cantidad de bocas del switch

⚠️ Estimación para el escenario (150 m², 3 pisos, 2 servidores).

| Dispositivo                                                             | Cant.                           | Velocidad    | PoE           | VLAN                    |
| ----------------------------------------------------------------------- | ------------------------------- | ------------ | ------------- | ----------------------- |
| Uplink al router                                                        | 1                               | 2,5 G / SFP+ | No            | Trunk                   |
| Servidor principal (datos)                                              | 1                               | 2,5 G        | No            | Trunk                   |
| Servidor principal (gestión remota BMC)                                 | 1                               | 1 G          | No            | Administración          |
| Servidor de backup (datos)? ==PORQUE ESTA SEPARAD CON GESTION REMOTA?== | 1                               | 2,5 G        | No            | Trunk (backup)          |
| Servidor de backup (gestión remota)                                     | 1                               | 1 G          | No            | Administración          |
| PC administradora                                                       | 1                               | 1 G          | No            | Administración          |
| AP piso 1, 2 y 3                                                        | 3                               | 1 G          | **Sí (PoE+)** | Gestión + VLAN por SSID |
| UPS (tarjeta de red/NUT)                                                | 1                               | 1 G          | No            | Administración          |
| KVM por IP / enchufe inteligente                                        | 1                               | 1 G          | No            | Administración          |
| Puerto de espejo para análisis                                          | 1                               | 1 G          | No            | Libre (mirror)          |
| Equipos opcionales (impresora, IoT, cámara)                             | 2                               | 1 G          | A veces       | Según caso              |
| **Subtotal en uso**                                                     | **14**                          |              |               |                         |
| **Reserva 25–40 %** (crecimiento, 2.º backup, repuestos)                | **+6**                          |              |               |                         |
| **Total recomendado**                                                   | **≈ 20 → switch de 24 puertos** |              |               |                         |

Sumá además **2 puertos SFP+** (no cuentan en los 24 de cobre).

---

## 1B.6 Puntos de acceso (APs)

### Cuántos

|Criterio|Cálculo|Resultado|
|---|---|---|
|**Cobertura**|Un AP de interior cubre ~60–100 m² efectivos con paredes|50 m² por piso → **1 AP por piso**|
|**Capacidad**|~25–40 clientes simultáneos por AP|Con ≤ 30 dispositivos por piso alcanza|
|**Pisos**|Una losa de hormigón atenúa 15–25 dB: el Wi-Fi no atraviesa pisos bien|**1 AP por piso, en el techo, al centro**|

⚠️ **Total: 3 APs.** Si un piso supera 40 dispositivos o tiene muros gruesos, sumá uno.

### Requisitos

|Característica|Requisito|Por qué|
|---|---|---|
|**Estándar**|Wi-Fi 6 (802.11ax) o superior|Mejor manejo de muchos clientes|
|**Alimentación**|**PoE** (802.3af/at)|Un solo cable de datos y energía|
|**VLAN por SSID**|Obligatorio|Separa _Hogar/Oficina_ e _Invitados_|
|**Aislamiento de clientes**|Para la red de invitados|Los invitados no se ven entre sí|
|**Seguridad**|WPA3 (o WPA2/WPA3), gestión sin nube obligatoria|Cifrado actual|
|**Conexión**|**Cableada (sin mesh inalámbrico)**|El mesh pierde velocidad en cada salto|
|**Gestión**|Controlador local (VM/contenedor) o AP autónomos|El controlador consume 1–2 GB de RAM si es una VM|
|**Planificación de canales**|2,4 GHz: canales 1/6/11; 5 GHz: 40 MHz sin solapamiento; **bajar la potencia**|Evita interferencia entre pisos|

---

## 1B.7 Otros equipos de administración de la LAN

| Equipo                                        | Función                                                                                                                                 | Conexión     |
| --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| **Router ISP**                                | Modo puente para gestion interna de LAN                                                                                                 |              |
| **Router administrable**                      | Control de la red                                                                                                                       | > 5          |
| **Switch administrable**                      | Gestión de las VLAN de la red -<br>1. VLAN INVITADOS<br>2. VLAN HOGAR<br>3. VLAN DMZ<br>4. VLAN ADMIN<br>5. VLAN APPS<br>6. VLAN BACKUP | > 5          |
| **Servidor productivo**                       | Hospeda las webs y herramientas de gestión                                                                                              |              |
| ----herramientas de control                   | 1. Adguard DNS - (VLAN ADMIN)<br>2. NGINX - (VLAN ADMIN)                                                                                |              |
| **PC administradora** (separada del servidor) | Única estación que gestiona Proxmox, switch y router, desde la VLAN de administración                                                   | 1 puerto 1 G |
| **Servidor de backup**                        | Copias independientes del principal                                                                                                     | 2,5 G + BMC  |
| **UPS con tarjeta de red o NUT**              | Apagado ordenado y alertas                                                                                                              | 1 puerto     |


> **Regla:** todo equipo de red y toda gestión (BMC, switch, router, APs) va en la **VLAN de administración**, nunca en la de usuarios.

---

## 1B.8 Servidor de backup en la red

|Aspecto|Requisito|Por qué|
|---|---|---|
|**Ubicación física**|**Otro cuarto, mejor otro piso**, con **toma y UPS propios**|Un incendio, una inundación o una sobrecarga en el rack no debe afectar a ambos|
|**VLAN**|Aislada (VLAN de backups), solo recibe conexiones del principal|Si el principal se compromete, el atacante no llega a las copias|
|**Velocidad de enlace**|2,5 GbE|La copia nocturna de ~1 TB baja a ~1 h|
|**Recursos** ⚠️|4 núcleos, 8–16 GB de RAM, discos ≥ 2× los datos protegidos|Menos CPU que el principal; la RAM sirve para la deduplicación|
|**Copia externa** (3-2-1)|Una tercera copia fuera del sitio|Se desarrolla en la pregunta 5|

---

## 1B.9 Cableado de cobre

|Categoría|Velocidad y distancia|Uso recomendado|
|---|---|---|
|Cat 5e|1 GbE / 2,5 GbE hasta 100 m|Mínimo aceptable|
|**Cat 6**|1 / 2,5 / 5 GbE hasta 100 m; 10 GbE hasta ~55 m|**Recomendado** para tendidos fijos|
|Cat 6A|10 GbE hasta 100 m|Si se quiere 10 GbE en cobre|
|Cat 7/8|No aportan en este escenario|No hace falta|

**Calidad:**

- **Cobre puro (no CCA)**, 23 AWG, para tendidos fijos. El CCA (aluminio recubierto de cobre) tiene más resistencia y falla con PoE.
- **U/UTP** alcanza en un hogar; **F/UTP (blindado)** solo cerca de fuentes de ruido y con buena puesta a tierra.
- **LSZH** (baja emisión de humo) si el cable va por conductos de uso común.
- Largo máximo del enlace: **90 m fijos + 10 m de patch = 100 m**.

**Cantidades para el escenario** ⚠️ (medí el recorrido real):

|Concepto|Cantidad|Detalle|
|---|---|---|
|Tendido fijo rack → AP|3 cables|Longitud medida + 15 % + 3 m de reserva|
|Tomas (keystone) en cielorraso|3|Una por AP|
|Patch panel|1 de 24 puertos|Ordena y protege los puertos del switch|
|Patch cords cortos (0,3–2 m)|~12|Router, switch, 2 servidores (datos + BMC), PC admin, UPS, KVM|
|Patch cord ONT → router|1|Cat 5e/6|
|Rack o gabinete pequeño|1|Con ventilación y organizador de cables|
|Tester de cables|1|Verificar cada tendido|
|Identificación|Etiquetas en ambos extremos|Evita errores al diagnosticar|

---

## 1B.10 Fibra óptica y módulos SFP

### ¿Hace falta?

En un hogar de 3 pisos, con tendidos < 90 m, **el cobre Cat 6 alcanza**. La fibra se justifica si:

- el tendido supera 90–100 m o va entre edificios,
- querés **10 GbE** entre equipos sin los problemas térmicos del cobre 10G,
- necesitás **aislamiento eléctrico** (la fibra no conduce) entre pisos o edificios,
- planificás crecer a un switch por piso con enlaces de 10 Gb.

### Tipos de fibra

|Tipo|Distancia 10G|Longitud de onda|Uso|
|---|---|---|---|
|**Multimodo OM3/OM4**|hasta 300 m / 400 m|850 nm|Enlaces internos dentro de un edificio|
|**Monomodo OS2**|hasta 10 km o más|1310 nm|Más larga distancia y futuro (soporta 25/100 G)|

### Módulos y cables directos

|Opción|Velocidad|Alcance|Notas|
|---|---|---|---|
|**DAC** (cable directo de cobre con SFP+)|10 G|≤ 3–5 m|**El más barato y de menor consumo para equipos en el mismo rack**|
|**AOC** (cable óptico activo)|10 G|3–30 m|Sin módulos separados|
|**SFP+ 10GBASE-SR** (multimodo, LC dúplex)|10 G|≤ 300 m (OM3)|Estándar interno|
|**SFP+ 10GBASE-LR** (monomodo)|10 G|≤ 10 km|Largas distancias|
|**SFP 1000BASE-SX/LX**|1 G|550 m / 10 km|Para puertos SFP de 1 G|
|**SFP+ RJ45 (10GBASE-T)**|10 G|30–80 m según cable|Se calienta (~2–3 W) y exige espacio entre módulos|

**Cuidados:**

- **Compatibilidad:** algunos switches solo aceptan módulos "codificados" del mismo fabricante. Revisá la lista de módulos soportados.
- Ambos extremos del enlace deben usar el **mismo tipo** (SR con SR, LR con LR) y **la misma longitud de onda**.
- **Radio de curvatura** mínimo ~30 mm. No doblar en ángulo cerrado.
- Mantener los **tapones de polvo** y limpiar los conectores antes de conectar.

---

## 1B.11 Energía de los equipos de red

Los equipos de red tienen que estar **detrás del mismo UPS** que el servidor: si el router o el switch se apagan, el servidor deja de ser accesible aunque siga encendido.

|Equipo|Consumo orientativo ⚠️|
|---|---|
|ONT / módem|5–10 W|
|Router|5–15 W|
|Switch 24 puertos (sin carga PoE)|15–40 W|
|3 APs por PoE|~45 W|
|**Total de red**|**~70–110 W**|

Sumalo al consumo del servidor al dimensionar el UPS ([[01_Equipamiento_recursos]], sección 1.4.7). La potencia total (servidor + red) es la base para elegir la capacidad del UPS.

---

## 1B.12 Checklist de compra de red

- [ ] **Router** propio con VLAN, firewall por zona, VPN WireGuard y **≥ 900 Mbps de NAT real**
- [ ] El equipo del ISP queda en **modo bridge**
- [ ] **Switch administrable de 24 puertos**, ≥ 8 PoE+, ≥ 6 de 2,5 GbE, 2 SFP+
- [ ] **Presupuesto PoE** ≥ 60–90 W
- [ ] **3 APs** Wi-Fi 6 con PoE y VLAN por SSID
- [ ] **UPS** de onda sinusoidal pura que cubra servidor + red
- [ ] **Cableado Cat 6** de cobre puro hacia cada AP, tomas y patch panel
- [ ] **Patch cords** cortos para el rack
- [ ] **DAC o módulos SFP+** solo si hay enlace de 10 G
- [ ] **Rack/gabinete**, tester y etiquetas
- [ ] **Servidor de backup** en otro cuarto, con su propia alimentación

---

## Pendientes para cerrar esta sección

- [ ] Medir el recorrido real de cada cable (rack → AP)
- [ ] Confirmar el **rendimiento NAT y VPN** del router elegido en su ficha técnica
- [ ] Confirmar que el firmware del router soporta **WireGuard**
- [ ] Registrar el consumo real del conjunto de equipos de red
- [ ] Decidir si se justifica fibra en tu instalación
- [ ] Pasar la configuración de VLAN y firewall a la pregunta 2

---

#### Referencias

- [[01_Equipamiento_recursos]] · [[Topología red]] · [[Configuracion VLAN - GUIA]] · [[Configuracion de red]] · [[Port Mirroring]]