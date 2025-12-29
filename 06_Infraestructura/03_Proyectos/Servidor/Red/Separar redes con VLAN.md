tipo:: #servidor 
tags::
fecha:: 2025-10-16
links::
links_externos::


Para eso necesitamos comprar un router que tenga la posibilidad de crear VLAN's y aislar redes. Esto requiere aprender algo de redes y de los dispositivos de conexion de internet.

|Característica|¿Por qué importa?|
|---|---|
|**Modo puente / bridge mode**|Para que el router del ISP deje de hacer router/NAT, y tu router maneje todo: DHCP, DNS, firewall, etc.|
|**Red de invitados (“Guest Network”)** con aislamiento|Para que los invitados solo tengan acceso a Internet y no puedan ver tu red local ni Ethernet.|
|**Administración de DNS personalizados**|Para poder elegir servidores DNS distintos a los del ISP, ya sea para rendimiento, privacidad o filtrado.|
|**Soporte para VLANs / subredes**|Para separar diferentes tipos de tráfico (invitados, LAN, servidores) fácilmente.|
|**Firmware flexible / opcional de terceros**|Si acepta firmware de terceros (como OpenWrt, DD-WRT, etc.), tendrás más control. Pero que de entrada tenga buenas opciones de firmware original ya está muy bien.|
|**Suficiente capacidad de hardware**|CPU decente, RAM, buen WiFi (sobre todo si querés 5 GHz y mucho tráfico), para evitar que el router se vuelva cuello de botella.|

https://www.mercadolibre.com.ar/router-vpn-tp-link-er7212pc-gigabit-3-en-1-omada-poe-color-negro/p/MLA27790573?matt_tool=38087446&pdp_filters=item_id%3AMLA1509529221&from=gshop&utm_source=chatgpt.com

![[Pasted image 20251016144457.png]]

Este router además de permitirme hacer lo que mencionamos arriba, me aproxima al siguiente paso de experimentar con la proveeduría de internet porque tiene habilitado puertos para conectores SFP de fibra multipunto para "repartir" el ancho de banda en varios router hacia distintos hogares.

https://www.youtube.com/watch?v=eisRlH114w8

## 🔧 **Configuración del Switch TP-Link SG2210MP**

### **Paso 1: Crear VLANs para Aislamiento**

bash

# En el switch (via Web UI):
VLAN 10: RED LOCAL (Tu casa principal)
VLAN 20: CASA 1
VLAN 30: CASA 2  
VLAN 40: CASA 3
VLAN 50: CASA 4
VLAN 60: RED GUEST (Invitados)

### **Paso 2: Configuración de Puertos**

text

Puerto 1 (Uplink): → Router Sagemcom (VLAN 10,20,30,40,50,60)
Puerto 2-5:        → Casas 1-4 (VLAN 20,30,40,50 respectivamente)
Puerto 6-8:        → Tu red local + servidores (VLAN 10)
Puerto 9-10:       → WiFi Guest/Invitados (VLAN 60)
SFP:               → [Opcional] Enlace inalámbrico punto-a-punto

### **Paso 3: Configuración DHCP por VLAN**

bash

# Rangos de IP separados:
VLAN 10 (Local):   192.168.10.1/24   (1-254)
VLAN 20 (Casa 1):  192.168.20.1/24   (1-254)
VLAN 30 (Casa 2):  192.168.30.1/24   (1-254)
VLAN 40 (Casa 3):  192.168.40.1/24   (1-254)
VLAN 50 (Casa 4):  192.168.50.1/24   (1-254)
VLAN 60 (Guest):   192.168.60.1/24   (1-254)

---

## 🏠 **Configuración para las 4 Casas**

### **Opción A: Cableado Directo (Ethernet)**

- **Cada casa** → Puerto dedicado en el switch
    
- **VLAN específica** por casa
    
- **Máximo aislamiento** y rendimiento
    

### **Opción B: Wireless Point-to-Point (Recomendado)**

bash

# Equipos necesarios por casa:
1x Access Point Ubiquiti NanoStation/LiteBeam
1x Router/AP en cada casa para distribución interna

**Configuración radio:**

- **Modo:** Station (en cada casa)
    
- **SSID:** Tu AP principal
    
- **Frecuencia:** 5GHz (menos interferencia)
    
- **Ancho de banda:** 40MHz o 80MHz
    

---

## 🛡️ **Reglas de Firewall y Aislamiento**

### **En el Switch TP-Link:**

bash

# Reglas de ACL (Access Control Lists):
1. PERMITIR VLAN 10 → INTERNET
2. PERMITIR VLAN 20 → INTERNET  
3. PERMITIR VLAN 30 → INTERNET
4. PERMITIR VLAN 40 → INTERNET
5. PERMITIR VLAN 50 → INTERNET
6. PERMITIR VLAN 60 → INTERNET
7. DENEGAR  VLAN 20 → VLAN 10,30,40,50,60
8. DENEGAR  VLAN 30 → VLAN 10,20,40,50,60
9. DENEGAR  VLAN 40 → VLAN 10,20,30,50,60
10. DENEGAR VLAN 50 → VLAN 10,20,30,40,60
11. DENEGAR VLAN 60 → VLAN 10,20,30,40,50
12. PERMITIR VLAN 10 → TODAS (para administración)

---

## 📊 **Gestión de Ancho de Banda**

### **Quality of Service (QoS) en el Switch:**

bash

# Límites por casa (ejemplo para 100Mbps total):
Casa 1: 20Mbps down / 5Mbps up
Casa 2: 20Mbps down / 5Mbps up  
Casa 3: 20Mbps down / 5Mbps up
Casa 4: 20Mbps down / 5Mbps up
Tu casa: 20Mbps down / 5Mbps up (prioridad alta)
Guest: 10Mbps down / 2Mbps up (prioridad baja)

---

## 🔌 **Configuración del Puerto SFP**

### **Para Extensión de Red:**

bash

# Opción 1: Fibra Óptica
- Conectar a otro switch en ubicación remota
- Ideal para distancias >100 metros

# Opción 2: Conversor SFP a Ethernet
- Extender red por cable ethernet largo
- Hasta 2km con fibra

# Opción 3: Radio Enlace
- SFP → Conversor → Radio Ubiquiti
- Para conexión inalámbrica de largo alcance

---

## 🏗️ **Ejemplo de Implementación**

### **Topología Física:**

text

[INTERNET FIBRA]
     |
[ROUTER SAGEMCOM] 
     |
[SWITCH TP-LINK SG2210MP]
     |      |      |      |      |
    [AP]   [AP]   [AP]   [AP]   [TU CASA]
   Casa1  Casa2  Casa3  Casa4   (VLAN 10)

### **Configuración IP:**

bash

# Router Principal (Sagemcom):
IP: 192.168.1.1
DHCP: Desactivado (el switch manejará DHCP)

# Switch TP-Link (Router principal ahora):
IP: 192.168.1.2
Gateway: 192.168.1.1
DNS: 8.8.8.8, 1.1.1.1

# Cada VLAN tendrá su propio DHCP

---

## ⚠️ **Consideraciones Legales/Técnicas**

1. **Verifica contrato ISP**: Algunos prohíben compartir conexión
    
2. **Ancho de banda**: Calcula 5-10Mbps por casa para uso normal
    
3. **SLA**: Define expectativas de servicio con tus vecinos
    
4. **Backup**: Considera internet de respaldo si es crítico

---
#### Referencias

