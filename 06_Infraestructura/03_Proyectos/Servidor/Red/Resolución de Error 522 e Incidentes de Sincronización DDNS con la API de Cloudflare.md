tipo:: 
tags:: #incidente #red #servidor  
fecha:: 2026-07-06
links:: [[Cloudflare]] [[Dynamic DNS (DDNS)]] [[Cloudflare DDNS]] [[Kuma Uptime]]
links_externos::
## 1. Resumen Ejecutivo

El presente informe documenta la investigación y resolución de una degradación de servicio crítica que afectó la disponibilidad de los servicios autohospedados expuestos a internet. El incidente se manifestó inicialmente con alertas de caída **HTTP Error 522 (Connection Timed Out)** en el sistema de monitoreo (_Uptime Kuma_), cuya causa raíz se identificó en el fallo persistente del agente automatizado de DNS Dinámico (DDNS) al intentar actualizar los registros de tipo `A` ante un cambio de direccionamiento IP pública por parte del ISP.

## 2. Cronología y Síntomas del Problema

1. **Alerta de Monitoreo:** El agente _Uptime Kuma_ arrojó alertas concurrentes de código de estado HTTP 522 para múltiples microservicios bajo el dominio `sanmartinici.com.ar`.
    
2. **Análisis de Capa de Red (Cloudflare Proxy):** El error 522 confirma que los servidores de borde de Cloudflare (_Edge Servers_) perdieron conectividad de capa 3/4 con el servidor de origen (nuestro _homelab_).
    
3. **Inspección de Logs del Contenedor DDNS:** Al auditar el contenedor encargado de la actualización dinámica de IP (`oznu/cloudflare-ddns` basado en la suite de inicialización `s6-overlay`), se detectó un bucle infinito de fallos de escritura:
    

Plaintext

```
Updating CloudFlare DNS record ejes.sanmartinici.com.ar from 186.39.144.246 to 181.20.8.60...
ERROR: Failed to update CloudFlare DNS record ejes.sanmartinici.com.ar from 186.39.144.246 to 181.20.8.60
crond: USER root pid 17615 cmd /etc/cont-init.d/50-ddns
```

> [!FAILURE] Diagnóstico Inicial El ISP asignó una nueva IP WAN (`181.20.8.60`), pero el registro DNS apuntaba a la IP vieja (`186.39.144.246`). Debido a esto, todo el tráfico colisionaba contra un nodo inexistente, generando el _timeout_.

## 3. Proceso de Diagnóstico y Depuración Técnica

Para identificar el motivo por el cual el script `/etc/cont-init.d/50-ddns` fallaba de manera persistente, se llevaron a cabo los siguientes pasos de descarte:

### Paso 1: Análisis de Encabezados HTTP (API de Cloudflare)

Se interceptaron las respuestas de la API de Cloudflare (`api.cloudflare.com`). Al analizar los metadatos de las cabeceras devueltas durante los intentos de negociación, se observaron las siguientes propiedades clave:

HTTP

```
date: Tue, 07 Jul 2026 01:13:44 GMT
cf-ray: a172fd00a8f21eb4-EZE
cf-cache-status: DYNAMIC
cf-auditlog-id: 019f3a23-5c86-71af-b551-ad2dddee07f1
api-version: 2026-07-07
```

- **Conclusión Parcial:** La presencia de `cf-auditlog-id` demostró que la petición alcanzaba con éxito la infraestructura de Cloudflare y generaba un evento de auditoría, descartando problemas de ruteo local o bloqueos de firewall saliente en nuestro host. No obstante, la conexión se cortaba inmediatamente después.
    

### Paso 2: Hipótesis de Expiración del API Token

Se sospechó de la caducidad implícita del token de acceso (OAuth Bearer). Para validarlo fuera del entorno aislado de Docker, se propuso realizar una llamada directa al _endpoint_ de verificación de la API mediante `curl`:

Bash

```
curl -X GET "https://api.cloudflare.com/client/v4/user/tokens/verify" \
     -H "Authorization: Bearer <CF_API_TOKEN>" \
     -H "Content-Type: application/json"
```

### Paso 3: Aislamiento del Nivel de Permisos (Lectura vs. Escritura)

Tras proceder a la renovación del token en el panel de Cloudflare configurando una expiración a largo plazo, los logs del contenedor mutaron, ofreciendo información crucial:

Plaintext

```
[cont-init.d] 30-cloudflare-setup: executing...
DNS Zone: sanmartinici.com.ar (b17c41ac7607cf2f54640de2fc40c36b)
DNS Record: ejes.sanmartinici.com.ar (7ec3abd7aea608fc77560c69fd5f5335)
[cont-init.d] 30-cloudflare-setup: exited 0.
[cont-init.d] 50-ddns: executing...
Updating CloudFlare DNS record ejes.sanmartinici.com.ar from 186.39.144.246 to 181.20.8.60...
ERROR: Failed to update CloudFlare DNS record ejes.sanmartinici.com.ar from 186.39.144.246 to 181.20.8.60
```

> [!INFO] Análisis del Log de Inicialización El script `30-cloudflare-setup` fue capaz de resolver los hashes internos de la **Zone** y del **DNS Record**. Esto significó que el nuevo token tenía permisos de **Lectura (Read)** válidos. Sin embargo, el script `50-ddns` fallaba inmediatamente al ejecutar el método `PUT / PATCH` para impactar la nueva IP.

El diagnóstico definitivo determinó un fallo en el alcance de las políticas del token (_Token Scopes_): se había asignado el permiso de **Zone.DNS:Read** en lugar de **Zone.DNS:Edit**, o la IP pública estaba bajo un _Rate Limit_ temporal debido al spam de peticiones previas del _cronjob_.

## 4. Solución Propuesta e Implementación

Para mitigar el incidente y restaurar la operación del clúster, se ejecutó un plan de acción dividido en tres fases:

### A. Corrección del Token ACL en Cloudflare

Se ingresó al panel de control de Cloudflare (**My Profile > API Tokens**) y se modificó la política de seguridad del token bajo los siguientes parámetros estrictos:

- **Permissions:** `Zone` -> `DNS` -> `Edit` (Permiso explícito de escritura).
    
- **Zone Resources:** `Include` -> `Specific Zone` -> `sanmartinici.com.ar`.
    
- **TTL:** Configurado sin fecha de expiración restrictiva a corto plazo para evitar interrupciones no programadas en el futuro.
    

### B. Forzado de Ejecución Manual (Bypass del Cron)

Dado que el demonio `crond` del contenedor entra en estado de suspensión (_sleep_) tras un fallo para respetar los intervalos planificados, se procedió a forzar manualmente la ejecución del script de actualización sin reiniciar la infraestructura de Docker:

Bash

```
docker exec -it <nombre_contenedor_ddns> /etc/cont-init.d/50-ddns
```

### C. Actualización de Contingencia en Panel Web

En paralelo, se modificó de manera manual el registro `A` del subdominio `ejes` apuntando a `181.20.8.60` para reestablecer de inmediato el puente de comunicación de Cloudflare y purgar las solicitudes en caché.

## 5. Estado Actual y Verificación

Tras aplicar los cambios en la matriz de permisos de la API y ejecutar el disparo manual, el agente procesó la solicitud de forma exitosa (`Exit 0`), sincronizando el estado local del servidor con los DNS globales.

> [!SUCCESS] Estado del Sistema Los servicios volvieron a estar operativos de forma externa, reflejando un estado **UP** (Verde) en el dashboard de _Uptime Kuma_. Queda bajo observación el comportamiento del contenedor ante el próximo ciclo de rotación de IP de la WAN para asegurar que el _job_ automatizado mantenga la persistencia sin intervención manual.



---
#### Referencias

