## 1.3 Método: del usuario al recurso

> [!info] Para qué sirve Convierte "cuántos usuarios" en **RAM, CPU, ancho de banda y disco**. Cada paso tiene una fórmula, un ejemplo con números y la forma de **medir** el dato real. Los valores típicos son un punto de partida ⚠️ que hay que reemplazar por mediciones propias ❓.

**Leyenda:** ✅ medido · ⚠️ estimado · ❓ por medir.

---

### 1.3.1 Tres niveles de "usuario"

|Nivel|Definición|Ejemplo|Dimensiona|
|---|---|---|---|
|**Registrados**|Personas con cuenta|200|Disco (cuotas), base de datos|
|**Simultáneos (N)**|Con sesión abierta y navegando|50 (pico 100)|**Todo lo demás**|
|**Peticiones en curso (L)**|Peticiones que el servidor está procesando en este instante|~5|RAM de workers y CPU|

Un usuario simultáneo no consume recursos todo el tiempo: lee, piensa y recién después hace clic. Esa pausa se llama **tiempo de reflexión (Z)** y es lo que hace que 100 usuarios generen pocas peticiones en curso.

---

### 1.3.2 Perfiles de usuario

No todos usan el sistema igual. Medí o estimá el reparto de tus usuarios:

|Perfil|Z (s entre clics)|Peticiones dinámicas por página ⚠️|Ejemplo|
|---|---|---|---|
|**Lector**|15–30|1 (CMS) a 5–10 (aplicación web)|Lee noticias o documentos|
|**Editor**|5–10|3–15|Edita, sube archivos, usa formularios|
|**Cliente de sincronización**|30–60, **continuo** (aunque la persona no esté)|1–2|App de escritorio o móvil de la nube de archivos|

> [!warning] Los clientes de sincronización cuentan aparte Un cliente de sincronización consulta al servidor cada 30–60 s **las 24 horas**, aunque el usuario no esté. 50 dispositivos sincronizando son ~1–2 peticiones/s permanentes.

---

### 1.3.3 Paso 1: de usuarios a peticiones

**Ley de respuesta interactiva** (forma exacta de la ley de Little):

```text
X = N / (R + Z)                      páginas por segundo
λ = X × d                            peticiones dinámicas por segundo
L = λ × R                            peticiones en curso (workers ocupados)
```

|Símbolo|Significa|Cómo se obtiene|
|---|---|---|
|**N**|Usuarios simultáneos|Objetivo del diseño (pico)|
|**Z**|Segundos entre clics|Tabla 1.3.2 o analítica|
|**R**|Tiempo de respuesta de una petición dinámica (s)|❓ Registro del servidor web|
|**d**|Peticiones dinámicas por página|❓ Pestaña "Red" del navegador|

⚠️ **Ejemplo (pico, un solo perfil de lectores con CMS):** N = 100, Z = 10 s, R = 0,5 s, d = 1.

- X = 100 / (0,5 + 10) = **9,5 páginas/s**
- λ = 9,5 × 1 = **9,5 peticiones dinámicas/s**
- L = 9,5 × 0,5 = **4,8 peticiones en curso**

**Con perfiles mezclados** se suma cada perfil por separado:

| Perfil         | N       | Z   | R   | X = N/(R+Z)       |
| -------------- | ------- | --- | --- | ----------------- |
| Lectores       | 70      | 20  | 0,5 | 3,4               |
| Editores       | 20      | 8   | 0,5 | 2,4               |
| Sincronización | 10      | 45  | 0,5 | 0,2               |
| **Total**      | **100** |     |     | **6,0 páginas/s** |

>[!NOTA] Las peticiones **estáticas** (imágenes, CSS, JS) se sirven desde el servidor web o la CDN y consumen mucho menos. Solo las **dinámicas** (PHP, Node, API) pasan por los workers. Esto es un ejemplo donde los lectores son mayoría, si la proporcion aumenta a Editores=60, Lectores=20, Sincronización=20 daría => 15 + 0,89 + 2 = **17,89 páginas/s**

#### Cuántos workers configurar

Las llegadas son aleatorias, no parejas: a veces entran varias peticiones juntas. Con llegadas aleatorias (Poisson), el 99 % del tiempo el número de peticiones en curso no supera:

```text
workers ≈ L + 2,33 × √L        (cubre el 99 % de los instantes)
```

|L (en curso)|Workers recomendados|Margen|
|---|---|---|
|4,8|10|×2,1|
|20|30|×1,5|
|50|67|×1,3|

⚠️ **Regla práctica:** margen de **×2 a ×3** en sistemas chicos y **×1,5** en grandes. Además, cuando el servidor se carga, **R sube** (la base de datos se disputa), lo que sube L: por eso conviene el margen alto al empezar.

> [!nota] Si te quedás corto Cuando se agotan los workers, las peticiones **hacen cola** y el usuario ve lentitud o errores 502/504. Si encima falta RAM, el sistema usa el disco como memoria (_swap_) y todo se frena. Es preferible fijar el máximo de workers según la RAM disponible y no más.

---

### 1.3.4 Paso 2: RAM

```text
RAM de una VM = SO base
              + (workers × RSS por worker)
              + buffers de base de datos
              + caché de aplicación (Redis, OPcache)
              + reserva de caché de archivos (20–30 %)
```

#### Valores típicos ⚠️ (medir en el servidor propio)

|Componente|Típico|De qué depende|
|---|---|---|
|SO base (Linux)|0,3–0,5 GB|Servicios instalados|
|Worker PHP (WordPress)|40–100 MB|Plugins, tamaño de página|
|Worker PHP (Nextcloud)|80–200 MB|Apps activas, vistas previas|
|Proceso Node o Python|50–150 MB|Aplicación; Node atiende muchas conexiones por proceso|
|OPcache (PHP, compartida)|128–256 MB|Cantidad de código|
|Buffer de base de datos|25–50 % del tamaño de la BD (mínimo 0,5–1 GB)|Conjunto de datos activo|
|Conexión a la base de datos|1–10 MB cada una|Tamaño de buffers por conexión|
|Redis|128–512 MB|Tamaño del caché configurado|
|Servidor web (nginx)|10–50 MB|Conexiones abiertas|

#### Ejemplo A: VM de CMS (WordPress + base de datos)

Con L = 4,8 → 15 workers (margen ×3), 80 MB cada uno.

|Concepto|GB|
|---|---|
|SO base|0,5|
|15 workers × 80 MB|1,2|
|OPcache|0,25|
|Buffer de MariaDB|1,0|
|Redis|0,25|
|nginx|0,1|
|**Subtotal**|**3,3**|
|**+ 25 % reserva de caché**|**≈ 4,1 → asignar 4–5 GB**|

#### Ejemplo B: VM de nube de archivos (Nextcloud)

20 workers (más peticiones por página y sincronización), 120 MB cada uno.

|Concepto|GB|
|---|---|
|SO base|0,5|
|20 workers × 120 MB|2,4|
|OPcache|0,25|
|Buffer de base de datos|1,5|
|Redis|0,5|
|Generación de vistas previas (ráfagas)|1,0|
|**Subtotal**|**6,2**|
|**+ 25 % reserva**|**≈ 7,7 → asignar 8 GB**|

#### Cómo medir el RSS real por worker ❓

```bash
# Memoria proporcional real (PSS) por proceso. Evita contar dos veces lo compartido
smem -P php-fpm -k

# Aproximación rápida con RSS (incluye memoria compartida, sobreestima)
ps --no-headers -o rss -C php-fpm8.2 | awk '{s+=$1;n++} END{print s/n/1024 " MB promedio, " n " procesos"}'
```

Medilo **bajo carga** (durante la prueba de la sección 1.9), no en reposo: el consumo de cada worker crece con las páginas más pesadas.

> [!nota] "Usada" no es "necesaria" Linux llena la RAM libre con caché de archivos. Para saber si falta memoria mirá **"disponible"** (_available_) y la actividad de _swap_, no el porcentaje de uso ([[Uso de memoria RAM por VMs]]).

---

### 1.3.5 Paso 3: CPU

```text
núcleos ocupados = λ × S              (S = segundos de CPU por petición)
núcleos necesarios = núcleos ocupados / utilización objetivo (0,6–0,7)
```

**S** no es lo mismo que R. R incluye esperas (base de datos, disco, red); S es solo el tiempo en el que la CPU trabaja de verdad. Se mide así:

```text
S = (CPU % de la VM × núcleos de la VM / 100) / peticiones por segundo
```

⚠️ **Valores típicos de S:**

|Petición|S (s de CPU)|
|---|---|
|Página de WordPress **sin** caché|0,2–0,5|
|Página de WordPress **con** caché de página|0,01–0,03|
|Petición de Nextcloud (interfaz o WebDAV)|0,1–0,4|
|API ligera (Node, Python)|0,01–0,1|
|Archivo estático|< 0,001|

#### Ejemplo (pico, λ = 9,5 peticiones/s)

|Caso|Núcleos ocupados (λ × S)|Con utilización del 65 %|
|---|---|---|
|WordPress sin caché (S = 0,3)|2,9|**4,4 núcleos**|
|WordPress con caché (S = 0,02)|0,19|**0,3 núcleos**|

> **La caché de página cambia la CPU en un factor de 10 a 15.** Es la optimización con mejor relación esfuerzo/resultado antes de comprar más hardware.

#### Carga fija que se suma siempre ⚠️

|Concepto|Núcleos|
|---|---|
|Host del hipervisor|0,5–1|
|Sistema de archivos con checksum/compresión (ZFS)|0,5–1|
|Monitoreo|0,3–0,5|
|Nube de archivos (sincronización constante)|0,5–1|
|**Backups (ráfaga, fuera del horario pico)**|**2–4**|

Los backups usan mucha CPU pero **no coinciden con el pico**: se programan de noche y no se suman al cálculo del pico.

#### De núcleos a hilos

Los hilos (SMT/Hyper-Threading) aportan ~25–30 % extra, no un núcleo completo. Se dimensiona en **núcleos físicos** y los hilos quedan como margen.

---

### 1.3.6 Paso 4: ancho de banda

```text
Mbps web   = X × peso de página (MB) × (1 − tasa de acierto de la CDN) × 8
Mbps total = Mbps web + Σ (transferencias grandes simultáneas × Mbps de cada una)
```

|Dato|Típico ⚠️|Cómo medirlo ❓|
|---|---|---|
|Peso de página (CMS sin optimizar)|1–3 MB|DevTools → Red → "transferido"|
|Peso de página optimizada|0,3–1 MB|Ídem|
|Tasa de acierto de la CDN|60–90 % en contenido estático|Panel de la CDN (solicitudes en caché)|
|Descarga o subida grande|20–100 Mbps cada una|Velocidad de la conexión del cliente|

⚠️ **Ejemplo (pico):**

- Web: 9,5 páginas/s × 2 MB × (1 − 0,7) × 8 = **46 Mbps** (con CDN) o **152 Mbps** (sin CDN).
- Transferencias grandes: 5 simultáneas × 50 Mbps = **250 Mbps**.
- **Total ≈ 300 Mbps** → con margen de ×1,5 → **~450 Mbps → contratar 500 Mbps–1 Gbps simétricos**.

> [!nota] Subida y bajada Lo que sale del servidor (respuestas a los usuarios) consume la **subida** del ISP. Es el valor limitante y el que hay que verificar.

**Conexiones y tabla NAT:** cada navegador abre 2–6 conexiones. 100 usuarios ≈ 600 conexiones, muy por debajo de lo que soportan nginx (decenas de miles) y la tabla de conexiones de un router de pequeña empresa.

---

### 1.3.7 Paso 5: disco

#### Operaciones por segundo (IOPS)

```text
IOPS = λ × (lecturas que no están en caché + escrituras) por petición
```

⚠️ **Ejemplo:** 9,5 peticiones/s × (1 lectura sin caché + 2 escrituras: sesión y log) = **~29 IOPS**.

|Disco|IOPS aproximados|¿Alcanza para ~30 IOPS?|
|---|---|---|
|HDD 7.200 rpm|100–200|Justo: los backups o una sincronización lo saturan|
|SSD SATA|50.000+|Sí, con enorme margen|
|NVMe|200.000+|Sí, con enorme margen|

> Por eso las **VMs van en SSD/NVMe**. El problema del HDD no es el promedio, sino que cualquier ráfaga (backup, reconstrucción, escaneo) lo deja sin capacidad para los usuarios.

#### Logs y métricas

```text
Logs/día = peticiones por segundo × bytes por línea × 86.400
         = 9,5 × 300 B × 86.400 ≈ 250 MB/día (sin comprimir; ~10× menos comprimido)

Prometheus/día = series × (86.400 / intervalo de muestreo) × ~2 B por muestra
               = 5.000 × (86.400 / 15) × 2 B ≈ 57 MB/día  → ~1,7 GB por 30 días
```

#### Capacidad de datos

```text
Capacidad = Σ (usuarios × cuota promedio) × (1 + crecimiento anual)^años × (1 / 0,8)
```

El último factor deja el sistema de archivos en **80 % de ocupación máxima**.

---

### 1.3.8 Paso 6: base de datos

```text
conexiones máximas ≥ workers totales + 20 % + conexiones de administración y monitoreo
```

Cada conexión abierta consume RAM (1–10 MB). **Más workers implica más conexiones y más RAM**, por eso el límite de workers se define una sola vez, mirando la RAM total.

---

### 1.3.9 Resumen: parámetros de entrada y resultados

|Parámetro|Valor de partida ⚠️|Medición real ❓|
|---|---|---|
|N|100 (pico)|Analítica|
|Z|10 s|Analítica de la aplicación|
|R|0,5 s|Registro del servidor web|
|d|1 (CMS) a 10|DevTools|
|RSS por worker|80–120 MB|`smem` bajo carga|
|S (CPU por petición)|0,02–0,3 s|Fórmula de 1.3.5|
|Peso de página|2 MB|DevTools|
|Acierto de CDN|70 %|Panel de la CDN|
|Transferencias grandes|5 × 50 Mbps|Registro de la nube|
|Operaciones de disco por petición|3|`iostat` bajo carga|

**Resultados del ejemplo (pico de 100 usuarios, un CMS):**

|Recurso|Resultado ⚠️|
|---|---|
|Peticiones dinámicas|9,5/s|
|Peticiones en curso (L)|4,8|
|Workers configurados|15|
|RAM de workers|1,2–1,5 GB|
|RAM total de la VM de CMS|4–5 GB|
|CPU (sin caché de página)|~4–5 núcleos|
|CPU (con caché de página)|~0,3 núcleos|
|Ancho de banda|~300 Mbps (≈ 450 Mbps con margen)|
|IOPS de disco|~30|

---

### 1.3.10 Calculadora

Para no repetir las cuentas a mano:

```python
def dimensionar(N=100, Z=10, R=0.5, d=1,        # usuarios, reflexión, respuesta, peticiones dinámicas por página
                S_cpu=0.3, rss_mb=100,            # CPU por petición, RAM por worker
                peso_pagina_mb=2, hit_cdn=0.7,    # peso y acierto de CDN
                descargas=5, mbps_descarga=50,    # transferencias grandes simultáneas
                io_por_peticion=3,                # operaciones de disco por petición
                margen=2.5, util=0.65):           # margen de workers y utilización objetivo de CPU
    X = N / (R + Z)                       # páginas por segundo
    lam = X * d                           # peticiones dinámicas por segundo
    L = lam * R                           # peticiones en curso
    workers = max(2, round(L * margen))
    return {
        "paginas_por_s":      round(X, 1),
        "peticiones_dinamicas_por_s": round(lam, 1),
        "en_curso_L":         round(L, 1),
        "workers":            workers,
        "ram_workers_gb":     round(workers * rss_mb / 1024, 2),
        "nucleos_cpu":        round(lam * S_cpu / util, 1),
        "mbps_web":           round(X * peso_pagina_mb * (1 - hit_cdn) * 8),
        "mbps_total":         round(X * peso_pagina_mb * (1 - hit_cdn) * 8 + descargas * mbps_descarga),
        "iops_disco":         round(lam * io_por_peticion),
    }

print(dimensionar())
# Con los valores por defecto: ≈ 9,5 páginas/s, 12 workers, ~1,2 GB para workers, ~4,4 núcleos, ~296 Mbps, ~29 IOPS
```

Reemplazá los valores por defecto con los medidos y ejecutá una vez por cada servicio (CMS, nube, aplicación propia).

---

### 1.3.11 Cómo medir cada dato en el servidor real

|Dato|Herramienta|Comando o lugar|
|---|---|---|
|**R** (respuesta)|Registro de nginx|Agregar `$request_time` al `log_format`; promediar con `awk '{s+=$NF;n++} END{print s/n}' access.log`|
|**L** (en curso)|Estado de PHP-FPM|`pm.status_path` → "active processes" y "max children reached"|
|**RSS por worker**|smem|`smem -P php-fpm -k` bajo carga|
|**S** (CPU por petición)|Monitoreo + prueba de carga|Fórmula de 1.3.5 con CPU% y X conocidos|
|**Ancho de banda**|Monitoreo de la interfaz o del router|Pico de Mbps durante la prueba|
|**IOPS y latencia**|iostat|`iostat -x 5` (columnas `r/s`, `w/s`, `await`)|
|**Tasa de acierto de CDN**|Panel de la CDN|Solicitudes en caché vs. totales|
|**Peso de página**|Navegador|DevTools → Red|

> [!nota] Proceso recomendado
> 
> 1. Calcular con los valores típicos y definir el hardware inicial.
> 2. Ejecutar la prueba de carga (sección 1.9).
> 3. Medir R, RSS, S, Mbps e IOPS reales.
> 4. Reemplazar los valores en la calculadora y ajustar el dimensionamiento.
> 5. Repetir cuando cambien las aplicaciones o el número de usuarios.