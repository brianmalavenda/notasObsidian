tipo::
tags::
fecha:: 2026-09-25
links::
links_externos::

## Contexto

El entorno de trabajo tiene una particularidad habitual en setups de desarrollo sobre Windows: **Ollama corre en el host Windows**, mientras que **los archivos de mi workstation viven en WSL2 (Ubuntu 24.04)**. Esto agrega una capa de complejidad de red que vale la pena documentar en detalle, porque es un problema recurrente y mal explicado en la mayoría de las guías.

> [!info] Resumen del entorno
> 
> - **Host**: Windows, con Ollama instalado nativamente (no en contenedor)
> - **Cliente**: WSL2 Ubuntu 24.04, entorno virtual de Python (`entorno_python`)
> - **Modelo**: `qwen2.5:3b-instruct-q4_K_M` (GGUF, cuantización Q4_K_M)
> - **Objetivo**: exponer la API REST de Ollama (puerto `11434`) desde el host hacia WSL2

---

## 1. Selección del modelo

### 1.1 Familia elegida: Qwen2.5

`Qwen2.5-Instruct` fue elegido sobre alternativas como Llama 3.2 o Gemma 2 por su desempeño comparativamente sólido en español para tareas de limpieza/formato de texto, algo consistentemente reportado por la comunidad de usuarios de Ollama para este tipo de pipelines de post-OCR.

|Variante|Tamaño (Q4_K_M)|VRAM aprox. requerida|Uso recomendado|
|---|---|---|---|
|`qwen2.5:3b-instruct-q4_K_M`|~2 GB|~3-4 GB|Primera opción — tarea mecánica, prioriza velocidad|
|`qwen2.5:7b-instruct-q4_K_M`|~4.7 GB|~6-7 GB|Si el modelo de 3B comete errores de reflujo notorios|

> [!tip] Sobre la cuantización Q4_K_M `Q4_K_M` es un punto de equilibrio estándar en el ecosistema GGUF: cuantiza los pesos a ~4 bits mientras conserva mayor precisión en las capas más sensibles del modelo (`K_M` = _K-quant, medium_). Para tareas de reformateo de texto —donde no se le exige razonamiento profundo al modelo— la pérdida de calidad frente a Q8 o F16 es prácticamente imperceptible, y el ahorro de VRAM/velocidad es sustancial.

### 1.3 Descarga e instalación en el servidor

```bash
# Descargar el modelo
ollama pull qwen2.5:3b-instruct-q4_K_M

# Confirmar que quedó disponible localmente
ollama list

# Prueba rápida de generación (interactiva, ctrl+d para salir)
ollama run qwen2.5:3b-instruct-q4_K_M
```

### 1.4 Verificación de la API REST local

Antes de tocar el tema de red con WSL2, conviene confirmar que el servicio responde correctamente **en el propio host**:

```bash
curl http://localhost:11434/api/tags
```

Una respuesta JSON con la lista de modelos instalados confirma que el servidor de Ollama está corriendo. Este paso es el baseline contra el cual comparar una vez que se intenta acceder desde WSL2.

---

## 2. El problema: WSL2 no llega a Ollama en el host

### 2.1 Por qué pasa esto

Por defecto, Ollama **bindea su servidor únicamente a `127.0.0.1`**, la interfaz de loopback. Esto significa que solo acepta conexiones que se originan en el mismo host — cualquier otra máquina (física o virtual) queda excluida, WSL2 incluido.

A esto se le suma una segunda capa de complejidad específica de WSL2: por defecto, **WSL2 opera en modo de red NAT**, donde la VM de Linux recibe una IP en una subred virtual distinta a la del host Windows. Esto tiene dos consecuencias prácticas:

- `hostname -I` ejecutado _dentro_ de WSL devuelve la IP de la interfaz virtual de WSL, **no** la del host Windows — es un error común asumir que esa IP sirve para conectarse "hacia afuera".
- La IP del host, vista _desde adentro_ de WSL, es la del **gateway** de esa subred NAT — y puede cambiar entre reinicios de WSL o del sistema.

> [!warning] Error común Setear `OLLAMA_HOST=0.0.0.0` como variable de entorno de sistema **no tiene efecto retroactivo** sobre un proceso que ya estaba corriendo. Es necesario reiniciar completamente el servicio de Ollama después de definirla.

### 2.2 Diagnóstico paso a paso

**Paso 1 — Confirmar en qué interfaz escucha Ollama** (desde PowerShell o CMD, en el host):

```powershell
netstat -ano | findstr 11434
```

- `127.0.0.1:11434 LISTENING` → Ollama solo acepta conexiones locales. Hay que corregir la variable de entorno y reiniciar el servicio (ver 2.3).
- `0.0.0.0:11434 LISTENING` → Ollama ya escucha en todas las interfaces; el problema está en el firewall o en el ruteo de WSL2 (ir directo al paso 3).

**Paso 2 — Reiniciar Ollama completamente** tras definir `OLLAMA_HOST=0.0.0.0`:

1. Click derecho en el ícono de la bandeja del sistema → _Quit Ollama_
2. Verificar en el Administrador de Tareas que no queden procesos `ollama.exe` / `ollama app.exe` colgados
3. Volver a iniciar Ollama (o correr `ollama serve` manualmente en una terminal para ver logs en vivo)
4. Repetir el `netstat` del paso 1

**Paso 3 — Regla de firewall de Windows.** El tráfico entrante desde WSL2 a veces cae en el perfil de red "Público" (_Public_), que Windows bloquea por defecto incluso para procesos que sí escuchan en `0.0.0.0`:

```powershell
# PowerShell como Administrador
New-NetFirewallRule -DisplayName "Ollama WSL" -Direction Inbound -LocalPort 11434 -Protocol TCP -Action Allow
```

**Paso 4 — Obtener la IP correcta del host, desde WSL2:**

```bash
ip route | grep default | awk '{print $3}'
```

Esa IP corresponde al gateway de la subred NAT de WSL2, que en la práctica es la dirección por la cual WSL2 accede al host Windows. Con esa IP se puede probar la conexión:

```bash
curl http://$(ip route | grep default | awk '{print $3}'):11434/api/tags
```

En el caso documentado acá, esa IP resultó ser `172.25.16.1` — valor que confirmó la conectividad una vez resueltos los pasos anteriores.

### 2.3 Solución robusta: modo de red _mirrored_

El enfoque de "buscar la IP del gateway" **funciona pero es frágil**: esa IP puede cambiar entre reinicios de WSL2, lo que rompe scripts o configuraciones que la tengan hardcodeada. Windows 11 (build 22H2 en adelante) introdujo un modo de red alternativo para WSL2 que elimina este problema de raíz.

**`networkingMode=mirrored`** hace que WSL2 comparta el mismo espacio de direcciones de red que Windows, en lugar de vivir en una subred NAT aislada. La consecuencia práctica es que `localhost` se vuelve intercambiable entre ambos entornos — ya no hace falta resolver ninguna IP de gateway.

Configuración, en `C:\Users\<usuario>\.wslconfig`:

```ini
[wsl2]
networkingMode=mirrored
```

Aplicar el cambio (desde PowerShell):

```powershell
wsl --shutdown
```

Y volver a abrir la distro. A partir de ahí, desde WSL2:

```bash
curl http://localhost:11434/api/tags
```

> [!note] Compatibilidad El modo _mirrored_ requiere Windows 11 22H2 o superior. En versiones anteriores de Windows, o en Windows 10, la única opción es el método de la IP de gateway (sección 2.2), idealmente combinado con algún mecanismo para no depender de que esa IP sea siempre la misma (por ejemplo, resolverla dinámicamente en el propio script, como se muestra en el paso 4).

### 2.4 Consideración de seguridad

Bindear un servicio a `0.0.0.0` lo expone a **toda** la red a la que esté conectado el host, no solo a WSL2 — algo particularmente relevante si el host Windows está en una red compartida (oficina, red doméstica con otros dispositivos, etc.). Para un entorno de laboratorio doméstico esto suele ser un riesgo aceptable, pero vale la pena tenerlo presente. Opciones para acotar la exposición:

- Restringir la regla de firewall del paso 3 a la subred específica de WSL2 (`172.25.16.0/20` o el rango que corresponda, visible con `ip addr` dentro de WSL) en lugar de permitir cualquier origen.
- Si el caso de uso lo permite, evaluar correr Ollama directamente en una VM Linux dedicada dentro de la misma red del host, evitando el salto Windows↔WSL2 por completo.

---

## 3. Validación del pipeline completo

Con la conectividad resuelta, la llamada desde el script de limpieza de texto (que usa la API `/api/chat` de Ollama vía `requests` en Python) queda así:

```bash
python3 clean_ocr_text.py libro.txt libro_final.txt \
    --model qwen2.5:3b-instruct-q4_K_M \
    --ollama-host http://172.25.16.1:11434 \
    --progress
```

Para validar el flujo de punta a punta sin esperar el procesamiento del libro completo, conviene primero correrlo sobre un recorte pequeño del archivo de texto:

```bash
head -c 20000 libro.txt > prueba.txt

python3 clean_ocr_text.py prueba.txt prueba_final.txt \
    --model qwen2.5:3b-instruct-q4_K_M \
    --ollama-host http://172.25.16.1:11434 \
    --progress
```

---

## 4. Conclusiones

- El binding por defecto de Ollama a `127.0.0.1` es la causa raíz más común de este tipo de problema de conectividad — conviene descartarlo primero con `netstat` antes de sospechar del lado de WSL2.
- La IP de gateway obtenida vía `ip route` en WSL2 es una solución funcional pero **frágil ante reinicios**; el modo de red `mirrored` (Windows 11 22H2+) es la solución más robusta y la recomendada para setups permanentes.
- Para tareas de post-procesamiento de texto acotadas (no generativas ni de razonamiento complejo), un modelo cuantizado de 3B es un punto de partida razonable antes de escalar a 7B — el ahorro en tiempo de procesamiento sobre un libro completo es significativo.

---

_Documentado como parte de un pipeline de digitalización de libros en español (OCR + limpieza asistida por LLM local)._