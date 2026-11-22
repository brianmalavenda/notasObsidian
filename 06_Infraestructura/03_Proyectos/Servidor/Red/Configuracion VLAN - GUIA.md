tipo::
tags:: #servidor/guia_instalacion 
fecha:: 2026-06-12
links::
links_externos::

## 1. Fundamentos y Taxonomía de Capa 2 / Capa 3

La segmentación lógica de redes en infraestructuras locales distribuidas se fundamenta en la disociación del dominio de difusión (Broadcast Domain) sin requerir la segregación de la infraestructura física subyacente.

* **VLAN (Virtual Local Area Network - IEEE 802.1Q):** Inyección de un identificador de 12 bits (VLAN ID) en la cabecera de la trama Ethernet (encapsulamiento `dot1q`). Permite aislar lógicamente el tráfico de Capa 2. Dispositivos en diferentes VLANs requieren estrictamente un enrutador o un switch de Capa 3 (*Inter-VLAN Routing*) para comunicarse, permitiendo la centralización del control de acceso mediante políticas de Firewall (ACLs).
* **Principio de Mínimo Privilegio en Redes:** Axioma de seguridad que establece que una subred o host específico solo debe poseer visibilidad y rutas de acceso hacia los recursos estrictamente necesarios para su función operativa. Toda zona residual no especificada debe ser gobernada por una política de denegación implícita (*Default Deny*).
		
## 2. Configuración Estructural del Switch: Modos de Puerto, PVID y Membresías

La correcta configuración de un switch administrable radica en la granularidad con la que se definen sus interfaces. La coexistencia de tráfico con etiqueta (*Tagged*) y sin etiqueta (*Untagged*) se gestiona bajo los siguientes parámetros:

### Modos de Operación de Puertos
1.  **Puerto de Acceso (Access Port / Untagged):** Diseñado para la conexión de hosts finales (Workstations, PCs de administración, dispositivos IoT) que no interpretan el protocolo IEEE 802.1Q. El switch remueve el tag de la VLAN al enviar la trama al host, e inyecta la etiqueta correspondiente cuando el host transmite hacia el switch.
2.  **Puerto Troncal (Trunk Port / Tagged):** Diseñado para la interconexión de dispositivos de red (*Switch-to-Switch*, *Switch-to-Router*) o hipervisores (*Switch-to-Proxmox*). Permite el transporte multiplexado de múltiples VLANs sobre un único enlace físico, manteniendo la integridad y aislamiento de las etiquetas en tránsito.

### PVID (Port VLAN ID) y Gestión de Tramas Nativas
El **PVID** define el identificador de VLAN asignado por defecto a cualquier trama entrante que carezca de etiqueta (tráfico *Untagged*). 

* **Puertos de Acceso:** El PVID debe coincidir exactamente con el ID de la VLAN operativa del puerto (ej. Puerto de PC Admin en VLAN 17 $\rightarrow$ PVID 17).
* **Puertos Troncales (Trunks):** El PVID mapea el tráfico de la **VLAN Nativa** (usualmente VLAN 1). El tráfico perteneciente a la VLAN Nativa se transmite por el troncal sin encapsulamiento dot1q, optimizando la compatibilidad con protocolos de control de red (STP, LACP, LLDP).

### Mitigación de Vulnerabilidades: El Concepto de `NOT MEMBER`
Por defecto, los switches inicializan todos los puertos como miembros *Untagged* de la VLAN 1. Mantener esta configuración expone la infraestructura a ataques de *VLAN Hopping* y contaminación de dominios de difusión. 
* **Práctica Operativa:** Todo puerto asignado de forma exclusiva a una VLAN específica debe configurarse explícitamente como `NOT MEMBER` en las demás VLANs del sistema. Si el puerto 2 pertenece a la VLAN 210 (Invitados), debe ser removido de la VLAN 1 y de cualquier otra zona crítica.

## 3. Topología de Red Segura y Flujo Inter-VLAN

[Módem ISP (Bridge)]
│ (IP Pública / PPPoE o DHCP)
[Router TP-Link / Gateway]
│
│ Puerto WAN / LAN
│ Interfaz Troncalizada (VLANs 17, 37, 57, 200, 210 Tagged)
▼
┌───────────┐
│  Port 1   │ ◄─── Puerto Trunk hacia el Gateway (Todas las VLANs Tagged)
│  Port 2   │ ◄─── Red INVITADOS (VLAN 210 Untagged, PVID 210)
│  Port 3   │ ◄─── Red HOGAR (VLAN 200 Untagged, PVID 200)
│  Port 4   │ ◄─── Red SANDBOX (VLAN 57 Untagged, PVID 57)
│  Port 5   │ ◄─── PC ADMINISTRACIÓN (VLAN 17 Untagged, PVID 17)
│  Port 6   │ ◄─── Servidor PROXMOX (VLAN 17, 37 Tagged, PVID 1)
└─────┬─────┘
│ El mismo cable físico multiplexa:
├─► Tráfico de Gestión e Infraestructura de Red Admin (VLAN 17)
└─► Tráfico de Servicios Públicos y Aplicaciones Prod (VLAN 37)

## 4. Virtualización Avanzada: Redes "VLAN-Aware" en Proxmox VE

Cuando un único servidor aloja instancias que pertenecen a diferentes zonas de seguridad (ej. Máquinas virtuales de infraestructura en la VLAN 17 y Contenedores LXC exponiendo servicios web en la VLAN 37), el hipervisor debe actuar como un switch virtual de Capa 2 embebido.

### Configuración del Bridge Virtual en Proxmox
Para habilitar el paso de tramas etiquetadas hacia el núcleo de Proxmox, la interfaz de puente principal (`vmbr0`) debe configurarse con la directiva `bridge-vlan-aware yes`. Esto le permite inspeccionar los tags dot1q y distribuirlos de forma selectiva.

Configuración óptima del archivo `/etc/network/interfaces`:

```text
# Interfaz física de red (NIC principal del servidor)
iface enp1s0 inet manual
    mtu 1500

# Linux VLAN-Aware Bridge
auto vmbr0
iface vmbr0 inet manual
    bridge-ports enp1s0
    bridge-stp off
    bridge-fd 0
    bridge-vlan-aware yes
    bridge-vids 2-4094
    mtu 1500

# Interfaz Virtual de Gestión (Host OS Proxmox en VLAN 17)
auto vmbr0.17
iface vmbr0.17 inet static
    address 10.50.1.100/24
    gateway 10.50.1.1

```

### Mecánica de Asignación en Instancias Virtuales

- **Segregación de Instancias:** Para asignar un contenedor LXC o una VM a la red de producción, basta con editar su hardware de red virtual en la interfaz de Proxmox e introducir el identificador `37` en el campo **VLAN Tag**. El switch virtual se encargará de etiquetar todo el tráfico saliente de esa instancia antes de enviarlo al puerto troncal físico (Puerto 6).
    
- **Ventaja de Seguridad:** Si un atacante compromete un contenedor en la VLAN 37, se encuentra confinado lógicamente en ese segmento. No podrá interceptar tramas de administración de la VLAN 17, dado que el Switch físico descartará cualquier intento de inyección de paquetes cruzados que no provenga de rutas autorizadas por el Firewall.
    

## 5. Estrategia de Almacenamiento y Respaldo (TrueNAS Core/Scale)

La integración de un entorno de almacenamiento masivo dentro de una infraestructura virtualizada exige delimitar el plano de gestión del plano de datos.

### El Riesgo del Backup "Inception" (Bucle Infinito)

Al respaldar sistemas operativos o hipervisores de manera automatizada a través de Proxmox (VZDump), se debe aplicar una exclusión explícita sobre los discos de datos masivos montados por passthrough o conexión directa (ej. unidades WD Red de 4TB en TrueNAS).

- **Fallo Operativo:** Intentar realizar un snapshot a nivel de hipervisor de un storage pool vivo de varios Terabytes colapsará el buffer de red y almacenamiento, resultando en un _IO Timeout_.
    
- **Solución Correcta:** Desmarcar la casilla de **Backup** en la pestaña de _Hardware -> Disk_ de Proxmox para las unidades de almacenamiento masivo. El respaldo de la VM de TrueNAS debe contemplar únicamente su disco raíz de sistema (ej. 32 GB), mientras que los datos puros se gestionan mediante réplicas nativas de ZFS o snapshots programados internamente en TrueNAS.
    

## 6. Ambigüedad Semántica en Equipos de Red: Port Trunking vs. VLAN Trunking

Es fundamental documentar y advertir a los equipos de ingeniería sobre la duplicidad del término **Trunk** en los paneles de administración de switches de gama media:

1. **VLAN Trunking (802.1Q):** Multiplexación de subredes lógicas sobre un cable físico mediante tags.
    
2. **Port Trunking / Link Aggregation (LACP / EtherChannel / Bonding):** Agregación física de múltiples interfaces de red en paralelo para incrementar el ancho de banda agregado y proveer tolerancia a fallos.
    

> ⚠️ **Error Crítico de Configuración:** Activar perfiles de "Port Trunking" (Estáticos o LACP) en un switch en puertos individuales donde solo se tiene conectado un único cable físico (hacia un servidor o router) generará loops lógicos e inestabilidad en la tabla MAC del equipo, provocando la pérdida total de conectividad. Esta pantalla debe permanecer sin configurar a menos que el host cuente con múltiples enlaces configurados explícitamente para agregación.