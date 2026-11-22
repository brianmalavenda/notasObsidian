
## Redes

1. **Red pública (①):** habilitá _client isolation_ en los access points para que los clientes no se vean entre sí.
2. Hoy cualquier cliente puede ignorarlo usando 8.8.8.8, 1.1.1.1 o DoH/DoT (Chrome, Firefox y muchas Smart TV lo hacen). Para que sea realmente obligatorio, necesitás reglas en el firewall/router. 
	1. Bloquear salida a los puertos 53 (TCP/UDP) y 853 (DoT) hacia internet, salvo desde AdGuard.
	2. Redirigir (DNAT) cualquier consulta al puerto 53 que salga de las VLANs hacia AdGuard.
	3. Bloquear los resolvers DoH conocidos (AdGuard tiene listas para esto) y, si es posible, bloquear el dominio `use-application-dns.net` para que Firefox desactive DoH.
2. La VLAN ② (Smart TVs) con “puerta trasera abierta” es tu mayor riesgo. Si las TV/TV Box tienen ADB abierto (puerto 5555, típico en TV Box Android baratas), son un punto de entrada trivial. Aislamiento total: ② solo puede salir a internet (y al DNS), **nunca** hacia ① ni ③.
	1. Escanear un TVBox de ejemplo y ver que puertos utiliza.
3. Portal cautivo o límites de ancho de banda en la pública, si corresponde.
4. |Origen|Destino|Permitido|
	|---|---|---|
	|①, ②, ③|AdGuard|UDP/TCP 53 (y 443/853 si usás DoH/DoT propio)|
	|①, ②, ③|NGINX|Solo 80/443|
	|NGINX|Apps en ③|Solo los puertos de cada app|
	|① y ②|Resto de ③|**Denegado** (solo vía NGINX)|
	|②|① y ③|**Denegado**|
	|①|②|**Denegado**|


## Aplicaciones

### NGINX

- **HTTPS real con certificados válidos:** usá un dominio propio (ej. `app.interno.tudominio.com`) y Let’s Encrypt con **DNS challenge** (funciona sin exponer nada a internet). Evitás las advertencias del navegador y cifrás el tráfico interno.
- **Access Lists** por app: restringí por IP/rango y/o autenticación básica en las apps más sensibles.
- **Headers de seguridad:** HSTS, `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`.
- **Bloquear acceso por IP directa:** configurá un host “default” que devuelva 444/404 para que no se pueda llegar a las apps por IP, solo por nombre.
- **Proteger el panel de admin** (puerto 81): que sea accesible solo desde una IP/VLAN de administración.
- **Activar “Block Common Exploits”** y WebSockets solo donde haga falta.
- Considerá **CrowdSec** o **Fail2ban** con los logs de NGINX para banear comportamientos abusivos.

### Adguard DNS

* ***Reescrituras DNS con comodín:** `*.interno.tudominio.com → IP de NGINX`. Así cada nueva app no requiere tocar AdGuard.
* **Alta disponibilidad del DNS:** si AdGuard cae, cae la navegación de toda la red. Tené una segunda instancia (otro LXC/VM, o incluso otro host) y entregá ambas por DHCP.


## Monitoreo

Uptime Kuma o Grafana/Prometheus para alertas de caída de servicios, certificados por vencer y disco.