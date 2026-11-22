tipo:: #servidor/hardware tags:: #wiki #homelab fecha:: 2026-11-17 links:: [[01_sanmartin701]], [[Velocidad de conexión de internet]], [[Uso de memoria RAM por VMs]], [[Topología red]] links_externos::

# Índice

[[#1. ¿Qué recursos necesito para un homelab de hasta 200 usuarios?]]
	[[#1.1 Qué significa "200 usuarios"]]
	[[#1.2 Recursos del servidor de producción]]
		[[#1.2.1 Placa madre]]
		[[#1.2.2 Otros componentes]]
	[[#1.3 Recursos por componente, con su porqué]]
		[[#1.3.1 CPU]]
		[[#1.3.2 Memoria RAM]]
		[[#1.3.3 Almacenamiento]]
			[[#Disco de sistema y VMs rápido y con baja latencia]]
			[[#Disco de datos capacidad y fiabilidad]]
			[[#Destino de backups independiente]]
		[[#1.3.4 Placa madre y plataforma]]
		[[#1.3.5 Red del servidor y red local]]
		[[#1.3.6 Conexión a internet]]
		[[#1.3.7 Energía y refrigeración]]
	[[#Consumo]]
	[[#1.4 Reparto de recursos por servicio (propuesta, 64 GB)]]
	[[#1.5 Niveles de escalado]]
	[[#1.6 Cómo elegir hardware que cumpla estos requisitos]]
	[[#1.7 Caso real de referencia (no es una recomendación)]]
	[[#1.8 Prueba de carga cómo validar el dimensionamiento]]
	[[#Pendientes para cerrar esta sección]]

# 1. Homelab, primeros pasos

 Esta guía describe **recursos y requisitos**, no productos. Cada componente se define por su **tamaño, tipo y velocidad**, y se explica **por qué** ese valor. Con eso podés elegir cualquier hardware que cumpla las especificaciones, nuevo o usado, de cualquier marca siempre teniendo una estimación sobre la cantidad de usuarios que apuntamos gestionar desde este servidor.

Tomamos como un ejemplo, una casuística de un total de "200 usuarios" que indica todas las personas con cuenta en las aplicaciones hospedadas en el homelab. Luego de esto hay que apuntar a aproximarnos a cuántas usan el sistema **a la vez**.

| Concepto                               | Valor                                              |
| -------------------------------------- | -------------------------------------------------- |
| Usuarios registrados (objetivo máximo) | 200                                                |
| Simultáneos hoy                        | ✅ ~20                                              |
| Simultáneos objetivo                   | 50                                                 |
| Picos                                  | 100                                                |
| Tipo de carga                          | Webs y aplicaciones consultadas desde el navegador |

>[!info] **Regla:** se dimensiona para el **pico** (100), no para el promedio, y después se verifica con una prueba de carga. Como hacer una prueba de carga con el sistema en funcionamiento es un desarrollo de otra sección de esta guía.
## 1.2 Recursos del servidor

### 1.2.1 Placa madre

| Recurso                                     | Por qué se necesita                                                                                                                                                                                                                                            | Notas para picos de 100 usuarios                                                                                                                                                                                                                      |
| ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **IPMI/BMC**                                | Permite **gestión fuera de banda**: encender/apagar/resetear, consola remota, montar ISOs por “virtual media”, ver logs y sensores sin monitor ni GPU dedicada. Es clave cuando varias personas administran el servidor o cuando está lejos físicamente.       | No añade rendimiento, pero reduce mucho el **MTTR** y evita desplazamientos. Para 100 usuarios en pico, la disponibilidad operativa importa. La ASRock X99 Taichi **no tiene IPMI**; alternativa: PiKVM/KVM IP + regleta inteligente.                 |
| **ECC RDIMM**                               | La memoria **RDIMM registrada con ECC** permite más capacidad y suele ser más barata por GB en plataformas de servidor usadas. ECC detecta y corrige errores de memoria, reduciendo cuelgues y corrupción de datos en servicios 24/7, ZFS, VMs y contenedores. | Con 100 usuarios en pico, más RAM ayuda a absorber concurrencia, caché y VMs. **Ojo:** muchas placas X99 —incluida la X99 Taichi— no soportan RDIMM; hay que verificar placa/CPU/QVL. No sustituye backups ni monitoreo.                              |
| **8–16 núcleos**                            | Los núcleos/hilos alimentan VMs, contenedores, servicios web, bases de datos, cifrado, compresión, backups y tareas de mantenimiento. También importa la **IPC**, la frecuencia y el soporte de **VT-d/IOMMU**, no solo el número de núcleos.                  | Para 100 usuarios en pico, 8–16 núcleos puede ser suficiente solo si la carga está bien repartida y no hay transcodificación/BD pesada. Si hay mucha concurrencia real, habrá que medir y probablemente subir o separar servicios.                    |
| **HBA dedicada**                            | Una controladora **SAS/SATA en modo IT** permite conectar muchos discos, pasar discos completos a TrueNAS/ZFS/Unraid, leer SMART correctamente y evitar RAID “fake” de placa. Mejora estabilidad y facilita el passthrough PCIe a una VM de almacenamiento.    | Para 100 usuarios, el almacenamiento suele ser el cuello de botella. No es obligatoria con 2–4 discos, pero sí muy recomendable para NAS con varios discos, backups o datos compartidos. Verifica que la placa tenga slots y líneas PCIe suficientes. |
| **VT-d/IOMMU, ACS y SR-IOV**                | Necesario para **passthrough** de HBA, NIC o GPU a VMs, y para virtualización de red más eficiente.                                                                                                                                                            | CPU y placa con VT-d/IOMMU activable; idealmente buen soporte ACS.                                                                                                                                                                                    |
| **Líneas y slots PCIe suficientes**         | HBA, NIC 10 GbE, GPU de transcodificación y NVMe compiten por líneas PCIe.                                                                                                                                                                                     | Al menos 2–3 slots utilizables además de M.2; revisar reparto de líneas según CPU.                                                                                                                                                                    |
| **Bifurcación PCIe**                        | Permite partir un slot x16 en varios x4 para NVMe o tarjetas adicionales.                                                                                                                                                                                      | Soporte de bifurcación x4/x4/x4/x4 si se prevén varios NVMe.                                                                                                                                                                                          |
| **NIC Intel, dual o 10 GbE**                | Mejor compatibilidad y estabilidad en Linux/Proxmox que muchas Realtek. Dual NIC permite separar gestión, VMs o firewall.                                                                                                                                      | Intel de doble puerto como mínimo; 10 GbE si hay NAS o muchos usuarios.                                                                                                                                                                               |
| **SATA/M.2/NVMe y arranque redundante**     | El sistema debe arrancar aunque falle un disco; los NVMe aceleran VMs y bases de datos.                                                                                                                                                                        | M.2 NVMe para sistema/VMs, SATA suficiente o HBA, y plan de arranque/backup.                                                                                                                                                                          |
| **Formato, ventilación y consumo idle**     | Un homelab 24/7 debe caber, refrigerarse y no disparar la factura. Placas ATX de entusiasta suelen consumir más en reposo.                                                                                                                                     | Medir consumo idle; preferir formato y ventilación adecuados al gabinete/rack.                                                                                                                                                                        |
| **BIOS/UEFI actualizable y QVL**            | Las plataformas usadas pueden necesitar BIOS concreta para ciertas CPU o RAM.                                                                                                                                                                                  | BIOS actualizable, dual BIOS si es posible, lista QVL de CPU/RAM y documentación.                                                                                                                                                                     |
| **Fuente de alimentación de calidad + UPS** | Más importante que la placa para disponibilidad. Un corte de luz puede corromper VMs o ZFS.                                                                                                                                                                    | PSU de calidad con margen; UPS con apagado ordenado.                                                                                                                                                                                                  |
| **Compatibilidad Linux/Proxmox/TrueNAS**    | El software define el homelab. Drivers de NIC, HBA y sensores deben funcionar.                                                                                                                                                                                 | Verificar comunidad y drivers antes de comprar; Proxmox VE como base estándar.                                                                                                                                                                        |
| **Sensores y control de ventiladores**      | Permite mantener ruido y temperaturas bajo control en 24/7.                                                                                                                                                                                                    | Lectura de temperaturas y control PWM aceptable en Linux.                                                                                                                                                                                             |
| **Documentación y reproducibilidad**        | Para varios colegas, una placa “modelo” debe poder replicarse y soportarse.                                                                                                                                                                                    | Misma revisión de placa, BIOS, CPU y RAM documentadas; evitar combinaciones exóticas.                                                                                                                                                                 |

### 1.2.2 CPU

| Parámetro                           | Requisito                                                                            | Por qué                                                                                                                                           |
| ----------------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Arquitectura**                    | x86-64 con extensiones de virtualización (VT-x/AMD-V) y paginación anidada (EPT/NPT) | Sin esto la virtualización es lenta o directamente no arranca                                                                                     |
| **IOMMU** (VT-d/AMD-Vi)             | Obligatorio si pensás pasar discos, tarjetas de red o GPU a una VM                   | Permite _passthrough_ y aislamiento seguro de dispositivos                                                                                        |
| **Núcleos físicos**                 | **8** (nivel ≤ 25 usuarios) · **12–16** (nivel objetivo)                             | Cada VM necesita vCPU, el host necesita 1–2 y los backups y la compresión generan ráfagas de 2–4 núcleos                                          |
| **Frecuencia**                      | **≥ 2,5 GHz sostenidos** con turbo hasta ~3,3 GHz o más                              | Las peticiones web (PHP, Node) se ejecutan en **un solo hilo**: **la latencia de cada página depende de la frecuencia, no del número de núcleos** |
| **Hilos (SMT)**                     | Valen como ~25–30 % extra de capacidad, no como núcleos completos                    | Mejoran el rendimiento en cargas mixtas, pero no se deben contar al 100 %                                                                         |
| **Aceleración de cifrado** (AES-NI) | Obligatorio                                                                          | El TLS en cada conexión y el cifrado de discos corren por hardware                                                                                |
| **Consumo (TDP)**                   | Lo más bajo que cumpla lo anterior                                                   | El servidor funciona 24/7: cada vatio sostenido cuesta ~8,76 kWh al año                                                                           |

>[!tip] **Sobreasignación (_overcommit_):** en CPU es aceptable asignar entre **2:1 y 3:1** de vCPU por núcleo físico para cargas web, porque las VMs rara vez usan CPU al mismo tiempo. **En RAM no se sobreasigna.**

### 1.2.3 Memoria RAM

|Parámetro|Requisito|Por qué|
|---|---|---|
|**Capacidad**|**32 GB** (≤ 25 usuarios) · **64 GB** (objetivo) · **128 GB** (crecimiento)|Es el recurso que primero se agota. Se suma lo de cada servicio (sección 1.5) y se deja 20–30 % libre|
|**Tipo ECC**|**Sí**|Los errores de memoria son poco frecuentes pero **silenciosos**: pueden corromper un archivo o una base de datos sin avisar. ECC los detecta y corrige. Pesa más aún con un sistema de archivos que usa la RAM como caché de escritura|
|**Velocidad (MT/s)**|La que soporte la plataforma; **no pagar de más**|Para cargas web la diferencia entre DDR4-2133 y DDR4-3200 es mínima (típicamente < 5 %). Importa mucho menos que la capacidad|
|**Canales ocupados**|**Todos** (ej.: 4 módulos en una CPU de 4 canales)|Con la mitad de los canales se **pierde la mitad del ancho de banda de memoria**, que es lo que realmente aprovecha el servidor|
|**Módulos**|Idénticos (mismo modelo, capacidad y velocidad)|Mezclar módulos fuerza a la velocidad más lenta y puede dar inestabilidad|
|**Ranuras libres**|Dejar al menos la mitad libre|Permite crecer sin descartar los módulos instalados|
|**Compatibilidad**|Verificar en el manual de la placa si acepta módulos registrados (RDIMM) o sin buffer (UDIMM). **No se pueden mezclar**|Un módulo incompatible no arranca|

>[!tip] **Regla de RAM:** el sistema operativo usa la memoria sobrante como caché de archivos. Por eso, para saber si falta memoria, mirá la columna **"disponible" (_available_)**, no "usada" (ver [[Uso de memoria RAM por VMs]]).

### 1.2.4 Almacenamiento

El detalle de configuración (RAID, ZFS, aplicaciones) ver en [[04 Guia almacenamiento]] acá solo detallamos los **recursos necesarios**. Se separan en tres funciones, porque tienen necesidades opuestas:

#### Disco de sistema y VMs: rápido y con baja latencia

| Parámetro                              | Requisito                             | Por qué                                                                                                                                                                |
| -------------------------------------- | ------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Tipo**                               | **SSD NVMe** (o SSD SATA como mínimo) | Las VMs y las bases de datos hacen miles de lecturas y escrituras pequeñas y aleatorias. Un HDD rinde ~100–200 IOPS; un SSD SATA ~50.000 o más; un NVMe, 200.000 o más |
| **Capacidad**                          | **~1 TB útil**                        | Suma de los discos de todas las VMs (~300–450 GB) + 40 % libre para snapshots y crecimiento                                                                            |
| **Redundancia**                        | **2 unidades en espejo**              | Es el disco donde corre todo: si falla y no hay espejo, caen todos los servicios                                                                                       |
| **Resistencia (TBW / DWPD)**           | **≥ 0,3 DWPD** (≈ 600 TBW por TB)     | Logs, bases de datos y snapshots escriben mucho. Con el sistema de archivos copy-on-write la escritura real puede multiplicarse por 3 a 5                              |
| **Protección ante corte de luz** (PLP) | Deseable                              | Evita perder escrituras sincrónicas de bases de datos                                                                                                                  |

#### Disco de datos: capacidad y fiabilidad

| Parámetro             | Requisito                             | Por qué                                                                                                                  |
| --------------------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Tipo**              | **HDD con grabación CMR** (no SMR)    | Los discos SMR reescriben por bandas y colapsan en reconstrucciones de arreglos RAID y ZFS                               |
| **Velocidad de giro** | 5.400–7.200 rpm                       | Para datos de archivos alcanza. Mayor rpm = más ruido, calor y consumo                                                   |
| **Capacidad útil**    | **≥ 2× los datos actuales**           | Deja crecer sin migrar. Con espejo, la útil es la mitad de la bruta. Con ZFS no se debe superar el **80 % de ocupación** |
| **Redundancia**       | **2 unidades en espejo**              | Tolera la falla de un disco. **No reemplaza al backup**                                                                  |
| **Rendimiento**       | ~150–250 MB/s secuenciales por unidad | Ya satura un enlace de 1 GbE (~117 MB/s). Las lecturas aleatorias dependen de la caché en RAM                            |
| **Clase**             | Discos pensados para NAS (24/7)       | Soportan vibración, funcionamiento continuo y tienen garantía acorde                                                     |

#### Destino de backups: independiente

| Parámetro     | Requisito                                        | Por qué                                                 |
| ------------- | ------------------------------------------------ | ------------------------------------------------------- |
| **Capacidad** | **≥ 500gb**                                      | Para guardar varias versiones y la deduplicación        |
| **Ubicación** | **Otro disco físico, mejor otro equipo o sitio** | Un backup en el mismo disco que el dato no es un backup |

>[!warning] **Tiempo de copia:** 1 TB a 117 MB/s (1 GbE) tarda ≈ 2,4 h; a ~290 MB/s (2,5 GbE) ≈ 1 h. Ese tiempo define la ventana de backup (se programa fuera del horario de picos).

### 1.2.5 Red del servidor y red local

| Parámetro                | Requisito                                                  | Por qué                                                                                                                                                                                                                         |
| ------------------------ | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Puertos del servidor** | **2 × Ethernet, ≥ 1 GbE** (ideal 2,5 GbE)                  | Un enlace de 1 GbE da ~940 Mbps útiles y alcanza para el tráfico web. 2,5 GbE ayuda en transferencias internas (nube, backups). Un segundo puerto sirve para separar gestión o redundancia                                      |
| **Switch**               | **Administrable**, con VLAN, trunk y ≥ 1 puerto de 2,5 GbE | Un switch no administrable no puede aislar redes. Soporte VLAN 802.1Q. La segmentación de red es una medida casi obligatoria                                                                                                    |
| **Router**               | Router VPN Multi-WAN                                       | Este router reemplazará la administración de paquetes de información del router del ISP que solo será un puente hacia la red local. Soporte VLAN 802.1Q. Es necesario que sea Gigabit para aceptar conexiones de alta velocidad |
| **Módulo SPF**           | De fibra a RJ45 o adaptador RJ45                           | Si tenemos cableado de fibra es necesario este módulo para conectar al router, si tenemos RJ45 pero el router acepta fibra, el módulo debe tener esa adaptación                                                                 |
| **Cableado**             | Cat 5e como mínimo (hasta 1 GbE), **Cat 6** para 2,5 GbE   | Cables de baja calidad generan errores y reducen velocidad                                                                                                                                                                      |

> [!nota] Tráfico interno Cuando el proxy y la aplicación están en el **mismo servidor y la misma red virtual**, el tráfico entre ellos no sale por el cable físico. Por eso el enlace físico dimensiona solo el tráfico hacia afuera y hacia el almacenamiento.

### 1.3.6 Conexión a internet

|Parámetro|Requisito|Por qué|
|---|---|---|
|**Velocidad**|**500 Mbps–1 Gbps** para 50–100 simultáneos|Navegación web ≈ 1–3 Mbps por usuario activo: 100 × 2 = ~200 Mbps. Sumando 5 descargas simultáneas de 50 Mbps = ~450 Mbps ⚠️|
|**Simetría**|**Subida igual a la bajada**|Para un servidor, lo que importa es la **subida**: son las respuestas que salen hacia los usuarios|
|**IP pública**|Fija o con DNS dinámico, **sin CGNAT**; o usar un túnel (Cloudflare Tunnel, VPN)|Con CGNAT no se pueden recibir conexiones entrantes directas|
|**Latencia y estabilidad**|Fibra óptica con pocos cortes|Mide más la experiencia que la velocidad pico|
|**Segunda salida** (opcional)|Enlace de respaldo|Elimina el punto único de falla de internet|

>[!info] **Medido:** plan contratado de 940 Mbps ([[Velocidad de conexión de internet]]). ❓ Falta confirmar la velocidad de **subida** real.

### 1.3.7 Energía y refrigeración

|Parámetro|Requisito|Por qué|
|---|---|---|
|**Fuente de alimentación**|Potencia tal que el consumo promedio quede al **30–60 % de la nominal**. Eficiencia **80 Plus Gold o superior**|Las fuentes son más eficientes en la zona media. Una muy sobredimensionada trabaja al 10–15 % con menor eficiencia|
|**Conectores**|Alimentación de CPU (EPS 8 pines) y SATA/PCIe suficientes|Es un error de compra común|
|**UPS**|Potencia en **W** (no solo VA) ≥ **2× el consumo medido**; **onda sinusoidal pura**; línea interactiva u online|Las fuentes con PFC activo pueden fallar con onda simulada|
|**Autonomía**|Suficiente para un **apagado ordenado** (5–10 min mínimo)|El objetivo del UPS es apagar bien, no seguir funcionando horas|
|**Apagado automático**|Software que apague las VMs y el host (ej.: NUT)|Sin él, la batería se agota y el corte es brusco|
|**Refrigeración**|Disipador acorde al TDP del CPU, flujo frontal-trasero y filtros|Un TDP de ~100–125 W necesita un disipador de torre. Objetivo: CPU < 80 °C, discos entre 30 y 45 °C|
|**Ruido**|Ventiladores grandes y lentos|El servidor suele estar en una vivienda|

#### Consumo de energía

> [!nota] Un servidor de este tamaño consume del orden de **100 W** (0,1 kW).

|Estado|Consumo orientativo ⚠️|
|---|---|
|Reposo (VMs encendidas, sin carga)|60–90 W|
|Carga media|100–150 W|
|Pico|200–250 W|

```text
kWh/mes = potencia (kW) × 24 h × 30 días
Ejemplo: 0,1 kW × 720 h = 72 kWh/mes
```

>[!warning] **Sugerencia:** enchufe inteligente con medición entre la pared y el UPS, registrando una semana.

## 1.4 Reparto de recursos por servicio (propuesta, 64 GB)

>[!nota] Reemplazá los valores por lo realmente asignado en tu servidor

| Servicio                                      | Tipo            | vCPU  | RAM                   | Por qué esa cantidad                                                     |
| --------------------------------------------- | --------------- | ----- | --------------------- | ------------------------------------------------------------------------ |
| Host del hipervisor                           | Host            | —     | 3–4 GB                | Sistema base y gestión                                                   |
| Caché del sistema de archivos (ZFS ARC)       | Host            | —     | hasta 8 GB (limitada) | Acelera lecturas; se limita a mano para que no consuma la RAM de las VMs |
| Almacenamiento (NAS)                          | VM              | 2     | 8 GB                  | ZFS necesita RAM para caché y metadatos                                  |
| Nube de archivos                              | VM              | 4     | 6–8 GB                | PHP-FPM + base de datos + Redis. Es el servicio más pesado               |
| CMS (WordPress, etc.)                         | VM o contenedor | 2–4   | 4 GB                  | Con caché de página reduce mucho la carga                                |
| Aplicación propia                             | VM              | 2–4   | 5,8 GB asignados      | ✅ **Medido: 1,3 GB de uso real** con carga real                          |
| Monitoreo (métricas y logs)                   | VM              | 2     | 3–4 GB                | Los logs crecen: definir retención                                       |
| Servidor de backups                           | VM              | 2     | 2–4 GB                | La deduplicación consume RAM                                             |
| Proxy inverso + DNS + salto de administración | LXC/VM          | 1 c/u | 0,5–1 GB c/u          | Livianos                                                                 |
| **Total asignado**                            |                 |       | **≈ 35–45 GB**        |                                                                          |
| **Libre**                                     |                 |       | **≈ 19–29 GB**        | Margen para picos y crecimiento                                          |

---

## 1.7 Caso real de referencia (no es una recomendación)

Configuración del autor, solo como dato de contraste:

|Recurso|Valor|
|---|---|
|CPU|14 núcleos / 28 hilos, 2,4–3,3 GHz, TDP 120 W|
|RAM|32 GB ECC (en ampliación a 64 GB)|
|Disco VMs|NVMe ~1 TB|
|Disco datos|2 × HDD 4 TB en espejo|
|Fuente|750 W (sobredimensionada para el consumo real)|
|Internet|940 Mbps contratados|
|Usuarios|✅ ~20 simultáneos|
|Consumo|⚠️ ~100 W estimado, sin medir|

---

## 1.8 Prueba de carga: cómo validar el dimensionamiento

❓ **Hoy no hay mediciones bajo carga.** Esta prueba cierra esa brecha. Se ejecuta **desde otra máquina** de la red local, con la herramienta gratuita **k6**.

```javascript
// prueba.js: simula usuarios navegando un sitio propio
import http from 'k6/http';
import { sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 20 },   // carga actual
    { duration: '5m', target: 50 },   // objetivo
    { duration: '5m', target: 100 },  // pico
    { duration: '2m', target: 0 },
  ],
  thresholds: {
    http_req_failed: ['rate<0.01'],      // menos de 1 % de errores
    http_req_duration: ['p(95)<1500'],   // el 95 % responde en < 1,5 s
  },
};

export default function () {
  http.get('https://TU-SITIO.ejemplo.com/');
  sleep(Math.random() * 10 + 5);  // 5–15 s entre clics
}
```

Ejecutar: `k6 run prueba.js`

**Qué registrar mientras corre** (capturas del monitoreo, pregunta 6):

|Métrica|Umbral aceptable|Si se pasa, el cuello de botella es…|
|---|---|---|
|CPU del host|< 70 % sostenido|CPU|
|RAM disponible|> 20 % libre|RAM|
|Latencia de disco|< 10 ms|Disco|
|Tráfico de red|< 70 % del enlace|Red o internet|
|Errores HTTP|< 1 %|Aplicación o configuración|
|Tiempo de respuesta p95|< 1,5 s|Cualquiera de los anteriores|

> Se ejecuta contra un sitio propio y en horario controlado. No apuntar a sitios de terceros.

---

## Pendientes para cerrar esta sección

- [ ] Completar la tabla 1.5 con lo realmente asignado a cada VM
- [ ] Medir el consumo real durante una semana (reemplaza 1.4.7)
- [ ] Ejecutar la prueba de carga y pegar las capturas del monitoreo (1.9)
- [ ] Ampliar la RAM a 64 GB y registrar el antes y después
- [ ] Confirmar la velocidad de **subida** real del enlace (1.4.6)
- [ ] Reconciliar los Mbps de [[01_sanmartin701]] (300–500) con [[Velocidad de conexión de internet]] (750–1000)


---

## Anexo

### Checklist: Cómo elegir hardware que cumpla estos requisitos

**Hoja de verificación antes de comprar** (revisá la ficha técnica de cada pieza):

- [ ] El CPU soporta **virtualización, IOMMU y AES-NI**
- [ ] La placa y el CPU soportan **RAM ECC** y el tipo de módulo (RDIMM o UDIMM) coincide
- [ ] La plataforma tiene **≥ 4 ranuras de RAM** y se pueden ocupar **todos los canales**
- [ ] Los discos de datos son **CMR** (no SMR) y de clase NAS
- [ ] El disco de VMs tiene **resistencia suficiente** (≥ 0,3 DWPD)
- [ ] Hay **suficientes puertos** SATA/M.2 para sistema, datos y backup
- [ ] Hay **2 puertos de red**, con chip bien soportado por el sistema operativo
- [ ] El switch es **administrable con VLAN**
- [ ] La fuente tiene **conectores y eficiencia** adecuados
- [ ] El UPS es de **onda sinusoidal pura** y tiene apagado automático
- [ ] Hay una **GPU básica temporal** si la plataforma no trae video
- [ ] El disipador y el gabinete **están a la medida del TDP**

> [!warning] Hardware usado Un solo servidor es un **punto único de falla**. Aceptarlo es válido para este tamaño, pero obliga a tener **backups probados** y un plan de reposición (pregunta 5).

### Niveles de escalado

|Nivel|Simultáneos|CPU|RAM|Disco VMs|Internet|
|---|---|---|---|---|---|
|**A: arranque**|hasta ~25|8 núcleos|32 GB ECC|500 GB–1 TB SSD|300 Mbps simétrico|
|**B: objetivo**|50 (picos de 100)|12–16 núcleos|**64 GB ECC**|1 TB NVMe en espejo|500 Mbps–1 Gbps simétrico|
|**C: crecimiento**|100+ sostenidos|2.º nodo o ≥ 24 núcleos|128 GB ECC|2 TB NVMe en espejo|1 Gbps simétrico|

>[!warning] Los niveles B y C son estimaciones. Se confirman con la prueba de carga.


---

#### Referencias

- [[01_sanmartin701]] · [[Velocidad de conexión de internet]] · [[Uso de memoria RAM por VMs]] · [[Topología red]]