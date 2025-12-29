tipo:: #servidor/red 
tags::
fecha:: 2025-10-16
links::
links_externos::


Tenemos un problema en la notebook cli-sanamrtin-09. Quisimos reservar un IP directamente desde el router para que el servidor DHCP tenga asignada una IP para la MAC Adress de la PC. Pero con varios reinicios de PC y de consultas de ipconfig | findstr "IPv4" sin tener más novedades que direcciones IP de la PC de esta forma 169.254.90.87 y/o 169.254.239.80. 

Ver TODOS los adaptadores y probar por este camino

Get-NetAdapter | Format-Table Name, Status, MacAddress, InterfaceDescription

Con esto optuvimos algo que puede ser el inidicio de lo que estaba fallando. Hay una red virtual que no tenía en cuenta, una Ethernet 2 que esta activada y que se llama VirtualBox Host-Only Ethernet Adapter mientras que el adaptador de red wifi de la PC, el físico, es MediaTek Wi-FI 6 MT7921 Wireless LAN Cred

Vamos a desactivar entonces la red virtual de Virtual Box a ver si con esto podemos resolver algo.
Tuve que "reestablecer la red", olvidar la red de wifi y apague la compu. La prendi, me conecte a la red, puse la contraseña y me asigno la IP bien.

#### Reasignar IP

```powershell
# Liberar la IP actual
ipconfig /release

# Renovar la IP
ipconfig /renew

# Ver la nueva IP
ipconfig | findstr "IPv4"
```

#### Resetear la red

```powershell
# Reset más profundo
netsh int ip reset
netsh winsock reset
ipconfig /flushdns

# Reinicia la notebook después de esto
```


### Qué es ARP? (Address Resolution Protocol)

**ARP es el "traductor" entre IPs y MAC addresses.**
#### Analogía simple:

- **IP Address** = Tu dirección postal (ej: Calle Principal 123)
- **MAC Address** = Tu DNI o identificación única personal
- **ARP** = El cartero que sabe que en "Calle Principal 123" vive Juan Pérez (DNI: XXXX)
**¿Porqué limpiar la cache ARP?** Porque tenía información **incorrecta o corrupta** - pensaba que tu IP 192.168.0.9 pertenecía a otra MAC address, causando el conflicto.

#### ¿Qué es un "Lease" DHCP?

**El "lease" (arriendo/alquiler) es el "contrato temporal" de IP que te da el router.**

```bash
# 1. DISCOVER (Descubrimiento)
PC: "¡Hola red! ¿Hay algún servidor DHCP disponible?"

# 2. OFFER (Oferta)
Router: "¡Sí! Te ofrezco la IP 192.168.0.9 por 24 horas"

# 3. REQUEST (Solicitud)  
PC: "¡Acepto la IP 192.168.0.9!"

# 4. ACKNOWLEDGE (Confirmación)
Router: "Confirmado. IP 192.168.0.9 asignada por 24 horas"
```

##### El problema de "Lease" que tenías:
**Escenario del conflicto:**
1. Tu router **reservó** 192.168.0.9 para tu notebook
2. Pero otro dispositivo **tomó esa misma IP** (lease activo)
3. Tu notebook intentó usar la IP **pero ya estaba ocupada**
4. **Resultado:** Ambas direcciones marcadas como "(Duplicado)"

### Wireshark

```bash
# Filtrar tráfico ARP sospechoso
arp.duplicate-address-frame

# Buscar respuestas ARP no solicitadas
arp.opcode == 2

```

## Puede ser que este corrupta la cache de ARP

```bash
# Detectar IP duplicada (tu caso podría ser esto)
ping 192.168.0.1
# Si responde pero el MAC no coincide con tu router → MITM

# Verificar gateway real
route print
netstat -r

# Scanner de red
nmap -sn 192.168.0.0/24
```

###  Prueba Rápida para Tu Red
```powershell
# 1. Verificar tu gateway real
ipconfig | findstr "Gateway"

# 2. Verificar MAC del gateway
arp -a | findstr "192.168.0.1"

# 3. Comparar con el MAC real de tu router
# (usualmente en una etiqueta en el equipo)

# 4. Escanear red en busca de dispositivos
arp -a
```

---
#### Referencias

