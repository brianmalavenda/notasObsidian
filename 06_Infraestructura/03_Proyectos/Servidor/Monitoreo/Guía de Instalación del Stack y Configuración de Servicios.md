tipo::
tags::
fecha:: 2026-06-19
links::
links_externos::

## 1. Estructura de Directorios (Aprovisionamiento Automatizado)

Para garantizar la persistencia de datos, tableros y fuentes, el stack utiliza un esquema de volúmenes compartidos en el host (`/docker/monitor-stack/`) estructurado como código:

Plaintext

```
/docker/monitor-stack/
├── alloy/
│   └── config.alloy               # Configuración del agente en sintaxis River
├── grafana/
│   ├── data/                      # Persistencia de base de datos de Grafana
│   ├── dashboards/                # Almacenamiento de archivos JSON de tableros
│   └── provisioning/              # Configuración automática al iniciar Grafana
│       ├── dashboards/
│       │   └── dashboards.yml     # Mapeo del directorio de tableros
│       └── datasources/
│           └── prometheus.yml     # Declaración de fuentes de datos (Prometheus/Loki)
├── prometheus/
│   └── prometheus.yml             # Reglas básicas de Prometheus
└── docker-compose.yml             # Definición de la infraestructura completa
```

## 2. Orquestación del Stack Central (`docker-compose.yml`)

Este manifiesto levanta el motor de visualización (Grafana), las bases de datos de series temporales (Prometheus, Loki) y los recolectores locales (Alloy, Node Exporter) dentro de una red aislada común.

```yaml
version: '3.8'

networks:
  monitoring:
    name: monitoring_network
    driver: bridge

services:
  # --- CAPA DE VISUALIZACIÓN ---
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    volumes:
      - /docker/monitor-stack/grafana/data:/var/lib/grafana
      - /docker/monitor-stack/grafana/dashboards:/var/lib/grafana/dashboards
      - /docker/monitor-stack/grafana/provisioning:/etc/grafana/provisioning
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin # Reemplazar en entornos de producción
    restart: unless-stopped
    networks:
      - monitoring

  # --- CAPA DE ALMACENAMIENTO ---
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    ports:
      - "9090:9090"
    volumes:
      - /docker/monitor-stack/prometheus:/etc/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--web.enable-remote-write-receiver'
    restart: unless-stopped
    networks:
      - monitoring

  # --- MOTOR DE LOGS ---
  loki:
    image: grafana/loki:latest
    container_name: loki
    ports:
      - "3100:3100"
    command: -config.file=/etc/loki/local-config.yaml
    restart: unless-stopped
    networks:
      - monitoring

  # --- CAPA DE RECOLECCIÓN (AGENTES) ---
  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    ports:
      - "9100:9100"
    privileged: true
    pid: host
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--path.rootfs=/rootfs'
    restart: unless-stopped
    networks:
      - monitoring

  alloy:
    image: grafana/alloy:latest
    container_name: alloy
    ports:
      - "12345:12345"
    volumes:
      - /docker/monitor-stack/alloy/config.alloy:/etc/alloy/config.alloy
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/log:/var/log:ro
    command:
      - 'run'
      - '--storage.path=/var/lib/alloy/data'
      - '/etc/alloy/config.alloy'
    restart: unless-stopped
    networks:
      - monitoring
```

## 3. Configuración del Agente (`alloy/config.alloy`)

El comportamiento interno de Alloy utiliza la sintaxis **River**. Se declaran los orígenes (_scrapes_), los componentes de procesamiento de texto (_pipelines_) y los canales de destino terminales (_receivers_).

```r
logging {
  level  = "info"
  format = "logfmt"
}

// ========================================================================
// FLUJO DE MÉTRICAS (PROMETHEUS)
// ========================================================================

// Destino: Endpoint de Prometheus expuesto por la bandera receiver
prometheus.remote_write "local_prometheus" {
  endpoint {
    url = "http://prometheus:9090/api/v1/write"
  }
}

// Origen: Recolección periódica sobre la interfaz de Node Exporter
prometheus.scrape "node_exporter" {
  targets = [{
    __address__ = "node-exporter:9100",
    instance    = "pro-monitor",
    environment = "produccion",
    service     = "system-metrics",
  }]
  forward_to = [prometheus.remote_write.local_prometheus.receiver]
}

// ========================================================================
// FLUJO DE LOGS (LOKI)
// ========================================================================

// Destino: Servidor de logs con inyección automática de metadatos globales
loki.write "local_loki" {
  endpoint {
    url = "http://loki:3100/loki/api/v1/push"
  }
  external_labels = {
    environment = "produccion",
  }
}

// Origen: Descubrimiento dinámico de ficheros de log generados por Docker Engine
local.file_match "docker_containers" {
  path_targets = [{
    __path__ = "/var/lib/docker/containers/*/*.log",
    job      = "docker-logs",
  }]
}

// Pipeline: Procesador y formateador de cadenas JSON en texto legible
loki.process "docker_json_parser" {
  stage.json {
    expressions = {
      output    = "log",
      stream    = "stream",
      timestamp = "time",
    }
  }

  stage.labels {
    values = {
      stream = "stream",
    }
  }

  stage.timestamp {
    source = "timestamp"
    format = "RFC3339Nano"
  }

  stage.output {
    source = "output"
  }

  forward_to = [loki.write.local_loki.receiver]
}

// Unión: Recolecta los archivos del buscador de archivos y los pasa al procesador
loki.source.file "docker_logs" {
  targets    = local.file_match.docker_containers.targets
  forward_to = [loki.process.docker_json_parser.receiver]
}
```

## 4. Archivos de Aprovisionamiento para Grafana

### Data Source de Prometheus (`provisioning/datasources/prometheus.yml`)

Este manifiesto fuerza la creación de los accesos internos sin requerir intervención manual desde la UI de Grafana:

```YAML
apiVersion: 1

datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://prometheus:9090
    isDefault: true
```

### Proveedor de Tableros (`provisioning/dashboards/dashboards.yml`)

Fuerza a Grafana a mapear y recargar continuamente cualquier archivo JSON guardado en la carpeta designada:

```YAML
apiVersion: 1

providers:
  - name: 'Tableros Automatizados'
    orgId: 1
    folder: 'Monitorización'
    type: file
    disableDeletion: false
    editable: true
    options:
      path: /var/lib/grafana/dashboards
```

## 5. Comandos de Inicialización y Despliegue Técnico

Para levantar la infraestructura limpia evitando errores de caché o desfasajes de lectura de logs en disco:

```Bash
# 1. Crear la estructura en el host si no existe
mkdir -p /docker/monitor-stack/{alloy,prometheus}
mkdir -p /docker/monitor-stack/grafana/{data,dashboards,provisioning/dashboards,provisioning/datasources}

# 2. Desplegar los contenedores forzando recreación limpia
docker compose down
docker compose up -d --force-recreate

# 3. Comprobar que no existan errores de compilación River o HTTP receivers
docker logs alloy
```


---
#### Referencias

