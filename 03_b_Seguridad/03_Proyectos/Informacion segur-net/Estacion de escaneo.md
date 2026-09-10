tipo::
tags::
fecha:: 2026-08-27
links::
links_externos::

# Propuesta — Estación de Escaneo y Sanitización de Dispositivos USB

**Contexto:** en el marco de la contingencia por el ataque a los servidores de nube de archivos, esta propuesta define la configuración, costo estimado y cronograma de implementación de una estación dedicada a escanear y sanitizar los pendrives que circulan entre docentes, alumnos investigadores y los nodos de resguardo de información.

---

## 1. Resumen ejecutivo

Se reutiliza un equipo existente (sin costo de adquisición) para construir una estación de arranque en vivo (_live boot_), sin persistencia, dedicada exclusivamente a inspeccionar y desinfectar pendrives antes de que su contenido llegue a los nodos de información. La inversión requerida es mínima (insumos menores) y el tiempo de puesta en marcha estimado es de **1 a 2 semanas** para la primera unidad funcional.

---

## 2. Especificación de hardware

### 2.1 Equipo base (ya disponible — costo $0)

|Componente|Especificación|
|---|---|
|CPU|Intel Celeron N4020|
|RAM|8 GB DDR4|
|Almacenamiento interno|SSD M.2 128 GB|
|Puertos USB|2x USB 3.0|

**Nota de capacidad:** el CPU es de gama baja (2 núcleos). Es funcional para el volumen actual, pero será el factor limitante si el volumen de archivos por alumno crece significativamente. Se recomienda monitorear tiempos de escaneo desde el primer mes de uso real.

### 2.2 Insumos adicionales requeridos

| Insumo                                                                                               | Función                                                                                | Cantidad                     | Costo aprox. (USD) |
| ---------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | ---------------------------- | ------------------ |
| Tarjeta microSD (con adaptador de bloqueo físico de escritura) o pendrive con switch de solo-lectura | Medio de arranque del sistema live, protegido contra escritura                         | 2 (1 en uso + 1 de respaldo) | USD 15–25          |
| Pendrive dedicado — "entrada" (marcado físicamente)                                                  | Puerto de recepción de pendrives no confiables                                         | 1                            | USD 5–8            |
| Pendrive dedicado — "salida/limpio"                                                                  | Puerto exclusivo para archivos ya sanitizados                                          | 2–3                          | USD 15–25          |
| Pendrive dedicado — "actualización de firmas" (uso exclusivo, nunca en manos de alumnos)             | Transporte semanal de bases de ClamAV desde la PC segura                               | 1                            | USD 5–8            |
| UPS pequeño (línea interactiva, 600VA aprox.)                                                        | Evitar corrupción de datos por corte de luz durante escaneo/transferencia              | 1                            | USD 40–70          |
| Etiquetas / precintos de tamper-evidence                                                             | Identificación física inequívoca de cada pendrive dedicado y detección de manipulación | 1 set                        | USD 5–10           |
| **Total estimado insumos**                                                                           |                                                                                        |                              | **USD 85–146**     |

_Los precios son aproximados (mercado internacional, en dólares) y deben confirmarse contra proveedores locales al momento de la compra, dado el efecto de tipo de cambio y disponibilidad en Argentina._

---

## 3. Configuración de software

### 3.1 Sistema operativo

- **Debian Live**, edición mínima **sin entorno gráfico** (solo consola), arrancado con la opción `toram` para que la imagen completa se cargue en RAM y el medio de arranque pueda operar en modo solo-lectura.
- Sin persistencia de estado entre reinicios, salvo la partición de configuración descripta en 3.4.
- Se elimina del sistema todo el stack de red (NetworkManager, wpa_supplicant, drivers Wi-Fi/Bluetooth) para reducir superficie de ataque, dado que el equipo es intencionalmente air-gapped.

### 3.2 Motores de detección

|Herramienta|Función|
|---|---|
|**ClamAV**|Motor antivirus principal, detección basada en firmas|
|**YARA**|Reglas de patrones para identificar familias de malware por comportamiento/cadenas, complementa a ClamAV|
|**PyCIRCLean / KittenGroomer** (librería, no la imagen oficial de Raspberry Pi)|Sanitización y reconstrucción de documentos: convierte archivos a formatos desarmados antes de copiarlos al pendrive de salida|

### 3.3 Políticas de montaje y control de dispositivos

- Montaje de todo medio externo con flags **`noexec`, `nosuid`, `nodev`** (el trío completo, no solo los dos originalmente previstos).
- Reglas **udev / USBGuard** para restringir el puerto de entrada exclusivamente a dispositivos de clase almacenamiento masivo, bloqueando cualquier intento de un pendrive de enumerarse como teclado u otro periférico (mitigación de BadUSB — no cubierta por los flags de montaje).
- Los puertos de entrada y salida quedan físicamente separados y etiquetados; nunca se usa el mismo puerto para ambos roles.

### 3.4 Persistencia controlada de firmas y logs

Para resolver la tensión entre "arranque efímero en RAM" y la necesidad de mantener firmas y registros:

- Una partición pequeña, separada del sistema live, almacena exclusivamente las bases de ClamAV (`.cvd`) y se actualiza semanalmente desde la PC segura mediante el pendrive dedicado a esa tarea. Cada actualización se valida con `sigtool --info` antes de importarse.
- Los registros de cada escaneo (resultado, archivos rechazados, alertas de YARA) se escriben a un log resumido en el pendrive de salida o en un medio de logging dedicado, revisado periódicamente — sin comprometer el diseño "sin persistencia" del sistema operativo en sí.

### 3.5 Verificación de integridad (complementaria a la detección de malware)

- Verificación de hash (SHA-256) de cada archivo contra la lista publicada por el docente, como capa adicional independiente del antivirus — detecta alteración de contenido aunque no sea identificado como malware conocido.

---

## 4. Cronograma de implementación

|Etapa|Actividad|Duración estimada|
|---|---|---|
|1|Preparación de la imagen base de Debian Live (mínima, sin GUI, sin stack de red)|1 día|
|2|Instalación y configuración de ClamAV + validación de actualización manual de firmas|1 día|
|3|Instalación y configuración de YARA + carga de reglas iniciales|1 día|
|4|Adaptación de PyCIRCLean/KittenGroomer al flujo de trabajo específico (conversión y copia a pendrive de salida)|2 días|
|5|Configuración de políticas de montaje (`noexec/nosuid/nodev`) y reglas udev/USBGuard|1 día|
|6|Configuración de partición de firmas/logs y script de actualización semanal|1 día|
|7|Pruebas funcionales (pendrives de prueba limpios e infectados controlados, medición de tiempos de escaneo)|2 días|
|8|Documentación del procedimiento operativo y capacitación breve a quien opere la estación|1 día|
|**Total**||**≈ 10 días hábiles (2 semanas)**|

_Estimación para una primera unidad funcional. Replicar la configuración en estaciones adicionales (si se decide tener más de una) toma significativamente menos tiempo una vez que la imagen base está validada — principalmente el tiempo de grabar el medio de arranque y ajustar etiquetado físico, del orden de 1 día por unidad adicional._

---

## 5. Consideraciones y limitaciones a comunicar

- Ningún antivirus ni motor de reglas garantiza detección al 100% — esta estación reduce el riesgo, no lo elimina. Se complementa con la verificación de hash y con las políticas de topología de distribución (pendrives dedicados, esquema en estrella) ya definidas en la hoja de ruta general.
- El hardware actual (Celeron N4020) es adecuado para el volumen de uso presente, pero deberá reevaluarse si el volumen de archivos o la cantidad de centros de investigación participantes crece significativamente.
- Esta estación resuelve la sanitización de dispositivos; no reemplaza los controles ya definidos a nivel de los nodos de información (arranque en vivo, disco de datos desconectado durante operaciones de riesgo, whitelisting de aplicaciones).

---

_Documento preparado como base de discusión para su aprobación por el área de Ciencias e Investigación._

---
#### Referencias

