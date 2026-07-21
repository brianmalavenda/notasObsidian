tipo::
tags::
fecha:: 2026-05-27
links::
links_externos::

### Capa 1 — Red externa (usuario → internet → tu IP)

**Método:** curl midiendo TTFB desde adentro y afuera de la red

```bash
curl -o /dev/null -s -w "Conectar: %{time_connect}s\nSSL: %{time_appconnect}s\nTTFB: %{time_starttransfer}s\n" https://ejes.sanmartinici.com.ar
```

**Resultado:** Conectar 5ms, SSL 22ms → ✅ **Descartado**. La red y el SSL son rápidos el problema esta en TTFB que es mayor a 18 segundos.

---

### Capa 2 — Nginx Proxy Manager (proxy reverso)

**Método:** curl directo al contenedor saltando NPM

```bash
curl -o /dev/null -s -w "TTFB: %{time_starttransfer}s\n" http://localhost:8080
```

**Resultado:** 0.058s interno vs 17s externo → ✅ **Descartado**. NPM no es el cuello de botella.

---

### Capa 3 — Base de datos MySQL

**Método:** Query Monitor + comandos directos en el contenedor

bash

```bash
docker exec -it wordpress_db mysql -u root -p -e "SHOW STATUS LIKE 'Slow_queries';"
```

**Resultado:** 0 slow queries, 0.053s total en DB → ✅ **Descartado**. MySQL es rápido.

---

### Capa 4 — PHP / WordPress

**Método:** Query Monitor → Overview **Resultado:**

- Page Generation Time: **17.55 segundos** ❌
- 100 queries a `wp_postmeta` en cascada
- HTTP API Calls: None ✅
- OPcache: instalado pero `opcache_get_status()` devolvía `false`

**Causa identificada:** El tema corp_news genera el patrón **N+1 queries** — por cada post en la home hace múltiples queries separadas a wp_postmeta para thumbnails, autor, categorías, etc.

---

### Capa 5 — Caché de objetos (Redis)

**Tareas:** Creamos el contenedor de Redis en el stack de wordpress. Luego instalamos el Plugin Redis Object Cache en wp y realizamos la prueba de conexión.

```bash
docker exec wordpress_app php -r "\$r = new Redis(); echo \$r->connect('redis',6379) ? 'OK' : 'FAIL';"
```

**Resultado:** Redis conectado ✅, 94.7% hit ratio ✅, queries bajaron de 100 a 57 — pero el tiempo **no mejoró** porque Redis cachea objetos PHP pero **no el HTML completo**.

---

### Capa 6 — W3 Total Cache

**Problema:** LiteSpeed Cache no puede servir HTML cacheado porque el contenedor usa **Apache**, no el servidor LiteSpeed. Sin full page cache, PHP regenera el HTML completo en cada request ejecutando las 57 queries cada vez.

**Tarea:** Instalar **W3 Total Cache** con Page Cache en modo Disk Enhanced, que sí funciona con Apache y sirve HTML estático sin tocar PHP ni MySQL. Plugin configurado con **Disk: Enhanced** como método de page cache. Al no usar servidor LiteSpeed, esta fue la única forma de servir HTML estático sin ejecutar PHP en cada request.

**Configuración aplicada:**
- Page Cache: ✅ Disk Enhanced
- Object Cache: ✅ Redis
- Browser Cache: ✅ habilitado
- TTL: 3600 segundos

---

### Mapa visual del problema

```
Usuario
   ↓
Internet          ✅ rápido (5ms TCP, 22ms SSL)
   ↓
Nginx Proxy Manager  ✅ rápido (no agrega latencia)
   ↓
WordPress/Apache     ❌ 17 segundos generando HTML
   ↓                    57 queries N+1 por tema corp_news
Redis               ✅ conectado pero solo cachea objetos
   ↓
MariaDB             ✅ rápido (0.053s total)
```

### Resultado final

| Métrica                        | Antes  | Después                 |
| ------------------------------ | ------ | ----------------------- |
| TTFB primera carga             | 17-18s | ~18s (genera caché)     |
| TTFB cargas siguientes         | 17-18s | **0.27 - 0.95s** ✅      |
| Queries por request            | 100    | 0 (sirve HTML cacheado) |
| Waiting for server (visitante) | 17.55s | **< 1s** ✅              |

### Notas para el futuro

- El caché no aplica a sesiones de administrador logueado — comportamiento normal y esperado
- Desactivar **Query Monitor** en producción — solo usarlo para diagnóstico puntual
- Próximo paso recomendado: activar **Cloudflare CDN** (nube naranja) para reducir aún más la latencia para visitantes geográficamente alejados del servidor
- El tema **corp_news** tiene un problema estructural de N+1 queries — considerar en el futuro migrar a un tema más eficiente o implementar un child theme con queries optimizadas

---
#### Referencias

