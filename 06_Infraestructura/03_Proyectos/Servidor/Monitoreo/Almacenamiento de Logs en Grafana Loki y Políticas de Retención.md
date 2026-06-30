tipo::
tags::
fecha:: 2026-06-19
links::
links_externos::

Este informe se centra en un concepto crucial para mantener la estabilidad del servidor a largo plazo: **cómo gestiona Loki el almacenamiento mediante archivos diarios** y cómo configurar las **políticas de retención (Retention)** para que los logs viejos se borren automáticamente y no te dejen la VM sin espacio en disco.

## 1. Filosofía de Almacenamiento de Loki: "Chunks" y Archivos Diarios

A diferencia de los sistemas tradicionales de bases de datos que indexan el texto completo de cada línea de log (lo que genera un consumo masivo de almacenamiento y RAM), Grafana Loki utiliza un enfoque optimizado de **micro-índices basados en etiquetas (labels)**.

El flujo de almacenamiento de Loki se divide internamente en dos estructuras críticas:

1. **Los Chunks (Bloques de datos comprimidos):** Loki agrupa las líneas de log crudas que comparten exactamente las mismas etiquetas, las comprime usando algoritmos de alta eficiencia (como gzip o snappy) y las guarda como objetos binarios cerrados (chunks).
    
2. **Los Índices:** En lugar de indexar el contenido del mensaje, Loki indexa únicamente el set de metadatos (por ejemplo: `environment="produccion"`, `job="docker-logs"`, `stream="stdout"`). Cuando realizás una consulta en Grafana mediante LogQL, el motor busca las etiquetas en el índice e inmediatamente extrae los chunks comprimidos correspondientes para leerlos en memoria a velocidad de ráfaga.
    

Para optimizar las operaciones de escritura y lectura en disco, Loki organiza estos chunks e índices en **períodos de tiempo predefinidos (habitualmente de 24 horas)**. Cada día a la medianoche (UTC), Loki cierra de forma definitiva los archivos del día anterior e inicia un bloque de archivos completamente limpio para la nueva jornada.

## 2. El Ciclo de Vida de los Logs y la Purga Automática (Compactor)

Cuando un contenedor genera miles de líneas por segundo, los archivos diarios acumulados en `/var/lib/loki/` pueden saturar el disco duro rápidamente. Para resolver esto de forma nativa, la arquitectura implementa el componente **Compactor** de Loki.

El Compactor es un subproceso interno dedicado a dos tareas principales:

- **Compactación:** Toma múltiples índices pequeños creados de forma dispersa durante el día y los consolida en archivos de índice únicos de gran escala para acelerar las búsquedas.
    
- **Aplicación del TTL (Time-To-Live):** Analiza la fecha de creación de los archivos diarios cerrados. Si un bloque diario supera el límite de antigüedad establecido en la política de retención, el Compactor elimina físicamente del disco todos los chunks e índices asociados a ese día de forma irreversible.
    

## 3. Configuración Técnica de Retención (`loki-config.yaml`)

Para activar la purga automática de logs viejos a los **7 días** de antigüedad, la configuración por defecto de Loki debe ser modificada en tu host. El bloque de control de retención requiere la declaración explícita de las secciones `limits_config` y `compactor`.

A continuación se expone el fragmento de configuración estructurado para el archivo de configuración de Loki:

```yaml
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9096

common:
  path_prefix: /var/lib/loki
  storage:
    filesystem:
      chunks_directory: /var/lib/loki/chunks
      rules_directory: /var/lib/loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

# ========================================================================
# POLÍTICAS DE RETENCIÓN Y LÍMITES (TTL)
# ========================================================================
limits_config:
  reject_old_samples: true        # Rechaza logs con marcas de tiempo muy viejas
  reject_old_samples_max_age: 168h # Ventana máxima de aceptación para ingesta (7 días)
  retention_period: 168h          # PERÍODO DE RETENCIÓN GLOBAL: Los logs se borran a los 7 días

compactor:
  working_directory: /var/lib/loki/compactor
  compaction_interval: 10m        # Intervalo en que se consolidan los índices
  retention_enabled: true         # ACTIVA EL PROCESO DE PURGA AUTOMÁTICA EN DISCO
  retention_delete_delay: 2h      # Tiempo de espera de seguridad antes de borrar físicamente
  retention_delete_worker_count: 15 # Hilos paralelos dedicados a la eliminación de archivos

schema_config:
  configs:
    - from: 2024-01-01
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h               # Tamaño del bloque del archivo de índice diario
```

## 4. Consideraciones Técnicas de Operación

- **Unidad de Medida (Horas):** En la directiva `retention_period`, los límites temporales se definen usando la nomenclatura estándar de Go (`h` para horas). Configurar `168h` equivale exactamente a mantener una ventana móvil de 7 días de almacenamiento de datos históricos.
    
- **El Error "Entry too far behind" (400 Bad Request):** Al activar `reject_old_samples`, Loki protege sus índices contra desórdenes cronológicos. Si un contenedor apagado durante días se enciende e intenta inyectar logs antiguos acumulados que superen las `168h` establecidas, Alloy recibirá un código de estado HTTP 400 y descartará esas líneas viejas de forma segura para proteger la integridad de los índices actuales.
    
- **Persistencia del Volumen en Docker:** Para que las políticas funcionen correctamente y no pierdas los metadatos del Compactor ante un reinicio del servidor, asegurate de tener mapeado el directorio de datos de Loki en tu `docker-compose.yml`:
    
    ```yaml
      volumes:
        - /docker/monitor-stack/loki/data:/var/lib/loki
    ```

---
#### Referencias

