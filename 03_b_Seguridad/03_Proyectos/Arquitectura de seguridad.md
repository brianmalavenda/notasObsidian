tipo::
tags:: #seguridad #vpn 
fecha:: 2026-06-16
links:: [[Que es una VPN]]
links_externos::

Este documento describe la arquitectura de seguridad propuesta para la infraestructura de la empresa, orientada a proteger los servicios expuestos a internet, asegurar la navegación de los usuarios remotos y establecer una postura de defensa en profundidad escalable. La propuesta se apoya en herramientas open source consolidadas, desplegadas sobre infraestructura propia basada en Proxmox VE con gestión de contenedores via Portainer.

La operación actual implica usuarios que se conectan de forma remota con notebooks propias, accediendo a servicios de la empresa (gestión documental, colaboración, comunicación) desde redes domésticas sin ningún tipo de control ni protección centralizada. Esto expone a la organización a tres vectores de riesgo principales:

**Riesgo de infección lateral.** Un equipo comprometido en una red no controlada puede convertirse en vector de ataque hacia los servicios internos de la empresa una vez que el empleado establece conexión.

**Ausencia de visibilidad.** Sin un punto de control centralizado, no es posible detectar actividad maliciosa, diagnosticar incidentes ni auditar el tráfico hacia los servicios corporativos.

**Exposición de servicios.** Los servicios públicos expuestos directamente a internet sin capas de protección intermedias son susceptibles a ataques de fuerza bruta, DDoS y explotación de vulnerabilidades conocidas.

---

## Arquitectura propuesta

La solución se estructura en cuatro capas independientes que operan en secuencia. La falla de cualquiera de ellas no compromete las restantes — principio de defensa en profundidad.

### Capa 1 — Protección perimetral: Cloudflare

Todo el tráfico hacia los servicios públicos de la empresa (Nextcloud, sitio web) pasa por la red de Cloudflare antes de llegar a nuestros servidores. Esto provee:

- Ocultamiento de la IP real del servidor de origen
- Mitigación automática de ataques DDoS volumétricos
- Web Application Firewall (WAF) con reglas para OWASP Top 10
- Terminación SSL/TLS con certificados gestionados automáticamente
- Rate limiting configurable por endpoint

La IP pública de la infraestructura nunca es expuesta directamente. Un atacante que intente escanear o atacar los servicios impacta primero contra la red de Cloudflare.

### Capa 2 — Autenticación centralizada: Authelia / Authentik

Un proxy de autenticación se interpone entre Cloudflare y todos los paneles de administración (Portainer, AdGuard Home, wg-easy, Proxmox). Cualquier acceso a estos servicios requiere:

- Credenciales válidas (usuario + contraseña)
- Segundo factor (TOTP via aplicación autenticadora)
- Sesión SSO unificada — un único login para todos los paneles

Esto elimina el riesgo de acceso no autorizado por credenciales comprometidas. Aunque un atacante obtenga una contraseña, no puede avanzar sin el segundo factor físico.

**Decisión pendiente:** Authelia es más liviano y simple de configurar; Authentik ofrece más funcionalidades (LDAP, SCIM, flows de onboarding) a costa de mayor complejidad operativa. Para el tamaño actual de la organización, Authelia es suficiente.

### Capa 3 — Segmentación de red: VLANs

La red interna está dividida en zonas con políticas de acceso estrictas entre ellas:

|VLAN|Propósito|Acceso desde internet|
|---|---|---|
|DMZ|Servicios expuestos, VPN endpoint|Solo puertos específicos|
|ADMIN|Proxmox, Portainer, AdGuard, monitoring|Nunca directo|
|LAN|Dispositivos de oficina|No|

El firewall del router (TP-Link Omada ER605) aplica ACLs entre VLANs. Un compromiso en la DMZ no permite movimiento lateral hacia la VLAN ADMIN donde reside la infraestructura de gestión.

### Capa 4 — Acceso remoto seguro: WireGuard + AdGuard Home

Los empleados remotos se conectan a la red corporativa exclusivamente a través de un túnel WireGuard. Una vez dentro del túnel:

- Todo su tráfico DNS es resuelto por AdGuard Home
- AdGuard bloquea dominios de malware, phishing, adware y telemetría en capa DNS antes de que se establezca cualquier conexión
- El tráfico sale a internet con la IP de la empresa, bajo las reglas de filtrado corporativas

**WireGuard** fue elegido sobre OpenVPN o IPsec por su menor superficie de ataque (código base de ~4.000 líneas vs ~100.000 de OpenVPN), mejor rendimiento (implementado en el kernel de Linux) y reconexión automática ideal para equipos móviles.

**AdGuard Home** actúa como primera línea de defensa ante amenazas de ingeniería social. Un empleado que recibe un email de phishing y hace clic en el enlace recibe un NXDOMAIN antes de que su navegador establezca cualquier conexión con el servidor malicioso.

---

## Consideraciones de privacidad

Dado que todo el tráfico de los empleados remotos transita por la infraestructura de la empresa, es necesario definir una política explícita de retención de logs. La propuesta es:

- **Query log de AdGuard:** desactivado. Solo se retienen estadísticas agregadas (cantidad de consultas bloqueadas por categoría, sin asociación a IP o usuario).
- **Logs de conexión WireGuard:** eventos de conexión/desconexión por 48 horas, sin volumen de tráfico ni contenido.
- Los peers de WireGuard se identifican con nombres neutros (device-01, device-02) sin asociación nominal en los archivos de configuración.

Esta política debe ser comunicada formalmente a los empleados como parte de una política de uso aceptable antes de la implementación.

---

## Stack tecnológico

| Componente              | Tecnología                 | Despliegue   |
| ----------------------- | -------------------------- | ------------ |
| Hipervisor              | Proxmox VE                 | Bare metal   |
| Gestión de contenedores | Portainer                  | Docker en VM |
| Colaboración            | Nextcloud (linuxserver.io) | Docker       |
| VPN                     | wg-easy (WireGuard)        | Docker       |
| DNS / filtrado          | AdGuard Home               | Docker       |
| Proxy inverso           | NGINX Proxy Manager        | Docker       |
| Monitoreo de uptime     | Uptime Kuma                | Docker       |
| DNS dinámico            | Cloudflare DDNS            | Docker       |
| Proxy perimetral        | Cloudflare                 | SaaS         |
| Router / firewall       | TP-Link Omada ER605        | Hardware     |

---

## Roadmap de implementación

### Fase 1 — Autenticación MFA (inmediata)

Despliegue de Authelia como proxy de autenticación delante de todos los paneles administrativos. Configuración de TOTP para cada administrador. Estimación: 1 jornada de trabajo.

### Fase 2 — Observabilidad (30-60 días)

Stack Prometheus + Grafana + Loki para métricas de infraestructura, logs centralizados y alertas. Integración con canal de Telegram o email para notificaciones de eventos críticos (contenedor caído, uso de CPU/RAM, intentos de login fallidos). Permite pasar de una postura reactiva a proactiva.

### Fase 3 — Detección de intrusos (60-90 días)

CrowdSec como IDS/IPS colaborativo. Detecta patrones de ataque (fuerza bruta, escaneo de puertos, explotación de CVEs conocidos) y bloquea automáticamente las IPs ofensivas en Cloudflare via API, antes de que el tráfico llegue al servidor.

### Fase 4 — GitOps y automatización (90-120 días)

Toda la configuración de infraestructura versionada en Gitea (instancia self-hosted). Ansible para provisioning reproducible. Ante un incidente que requiera reconstruir un servicio, el tiempo de recuperación se reduce de horas a minutos con un estado conocido y auditado.

---

## Análisis de riesgo residual

Ninguna arquitectura elimina el riesgo completamente. Los vectores que esta propuesta no cubre y que deben ser considerados:

**Compromiso de credenciales con acceso físico al dispositivo TOTP.** Si un atacante obtiene acceso físico al teléfono de un administrador, puede bypasear MFA. Mitigación parcial: políticas de bloqueo de pantalla y cifrado de dispositivo.

**Vulnerabilidades de día cero en los servicios desplegados.** Nextcloud, AdGuard y demás servicios pueden tener CVEs no parcheados. Mitigación: política de actualización de imágenes Docker cada 30 días y suscripción a los canales de seguridad de cada proyecto.

**Ingeniería social directa.** El filtrado DNS no protege contra un empleado que entrega sus credenciales voluntariamente. Mitigación: capacitación periódica en seguridad.

---

## Conclusión

La arquitectura propuesta implementa defensa en profundidad con herramientas de bajo costo operativo y alta madurez técnica. Cada capa es independiente, auditable y escalable. El roadmap permite incorporar capacidades de observabilidad y detección activa de forma progresiva sin interrumpir la operación existente.

Se recomienda avanzar con la Fase 1 (Authelia) como prerequisito para cualquier otra mejora, dado que sin autenticación fuerte en los paneles de administración el resto de las capas de seguridad pueden ser bypasseadas por acceso directo a la gestión.


---
#### Referencias

