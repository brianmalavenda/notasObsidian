tipo::
tags::
fecha:: 2026-06-19
links::
links_externos::
## 1. Introducción y Contexto

Este documento detalla el diseño de la arquitectura para el stack de monitoreo, recolección de métricas y consolidación de logs implementado en el servidor principal (`vm-monitor`).

El objetivo fundamental es establecer un sistema robusto, modular y de bajo consumo de recursos capaz de observar la infraestructura local y expandirse de forma transparente hacia otros nodos de la red en fases posteriores.

## 2. Diagrama Conceptual del Flujo de Datos

El flujo de la información se divide en dos ciclos independientes (Métricas y Logs) administrados por un único agente recolector centralizado:

![[Pasted image 20260619093654.png]]
## 3. Componentes de la Arquitectura

La solución se compone de cinco contenedores Docker integrados en la misma red virtual, garantizando aislamiento y resolución de nombres nativa:

|**Componente**|**Rol en la Arquitectura**|**Puerto Interno**|**Mecanismo de Comunicación**|
|---|---|---|---|
|**Node Exporter**|Exposición de métricas de hardware del Host (CPU, RAM, Disco).|`9100`|Pull (Scrape) desde Alloy.|
|**Docker Engine Logs**|Archivos contenedores `.log` en el file system de la VM.|N/A|Lectura directa de disco por Alloy.|
|**Grafana Alloy**|Agente unificado de procesamiento y ruteo de datos.|`12345`|Envío activo (Push) hacia Prometheus/Loki.|
|**Prometheus**|Base de datos de series temporales (Métricas).|`9090`|Recibe via `remote_write` (Receiver activo).|
|**Loki**|Datastore optimizado para logs indexados por etiquetas.|`3100`|Recibe via HTTP API (`/loki/api/v1/push`).|

## 4. Decisiones Críticas de Diseño e Implementación

### A. Adopción de Grafana Alloy (Sintaxis River)

Se optó por reemplazar el flujo tradicional (Promtail + agentes dispersos) por **Grafana Alloy** utilizando su nuevo motor de configuración basado en **River**.

- **Ventaja:** Permite construir pipelines modulares y programables. Las referencias entre componentes se realizan mediante variables de entorno internas y objetos interconectados (ej. `forward_to = [loki.write.local_loki.receiver]`), eliminando la redundancia de código y optimizando el uso de memoria en el host.
    

### B. Descubrimiento y Pipeline de Procesamiento de Logs de Docker

Los logs de Docker se escriben en formato JSON plano nativo en el host, lo que introduce "ruido" visual innecesario. Se diseñó un pipeline de procesamiento en tiempo real dentro del agente que realiza las siguientes tareas cronológicas:

1. **Extracción:** Parseo del JSON para aislar el mensaje crudo (`log`), el canal de salida (`stream`) y el tiempo original (`time`).
    
2. **Etiquetado:** Inyección de metadatos dinámicos como etiquetas de Loki para búsquedas instantáneas.
    
3. **Sincronización Temporal:** Reemplazo del timestamp de lectura por el del evento original para evitar desgloses incorrectos en Grafana.
    
4. **Sanitización:** Reescritura de la salida eliminando la envoltura JSON para entregar texto limpio al operador.
    

### C. Habilitación del Receptor Remoto en Prometheus

Por defecto, Prometheus opera bajo un modelo de _Pull_ (él sale a buscar las métricas). Para integrar la flexibilidad de Alloy, se modificó la política de inicio del servidor Prometheus activando la bandera:

Bash

```
--web.enable-remote-write-receiver
```

Esto transforma a Prometheus en un receptor pasivo capaz de almacenar las métricas compactadas que Alloy le envía mediante ráfagas HTTP (Remote Write), reduciendo drásticamente el tráfico cruzado de red.

> ⚠️ **Nota de Infraestructura (Seguridad en Proxmox VE):**
> 
> Al correr Node Exporter dentro de un entorno Docker aislado sobre una VM de Proxmox con cortafuegos activo, se determinó mandatorio asignar directivas de ejecución privilegiada (`privileged: true`) y acceso directo al espacio de nombres de procesos del host (`pid: host`). Sin estas directivas, las llamadas del contenedor al kernel de Linux (`/proc` y `/sys`) son interceptadas, resultando en métricas vacías (_No Data_).

## 5. Consideraciones para la Fase 2 (Escalabilidad de la Red)

La arquitectura está diseñada para escalar hacia un entorno multi-nodo de la siguiente manera:

- El stack central (Prometheus, Loki, Grafana) permanecerá estático en esta VM de Monitoreo.
    
- Para monitorear el hipervisor Proxmox u otras VMs de aplicaciones, solo se requerirá desplegar una instancia ligera de **Grafana Alloy** y **Node Exporter** en cada destino objetivo.
    
- Dichos agentes remotos apuntarán sus directivas de salida hacia las IPs de los contenedores centralizados de esta máquina, atravesando las distintas subredes/VLANs correspondientes.


---
#### Referencias

