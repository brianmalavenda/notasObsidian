tipo:: [[Seguridad Web]] 
tags:: #seguridad/web/cache #web/seguridad 
fecha:: 2025-08-29
links::
links_externos::


Este ataque consiste en **manipular o "envenenar" un caché web (como el de un CDN o un proxy)** para que almacene una respuesta HTTP maliciosa y luego la sirva a múltiples usuarios. El objetivo es distribuir contenido dañino a gran escala sin comprometer directamente el servidor de origen 210.

#### 📝 **Ejemplo Práctico de Prueba (Para Aprendizaje)**

Muchos sitios web utilizan la cabecera HTTP `X-Forwarded-Host` para saber qué host original quería el usuario, quizás para generar URLs absolutas o para fines de analytics.

1. Un atacante descubre que el sitio `victima.com` genera una respuesta que incluye el valor de la cabecera `X-Forwarded-Host` sin sanitizarla:
    
    http
    
    GET / HTTP/1.1
    Host: victima.com
    X-Forwarded-Host: atacante.com
    
2. El servidor, vulnerable, responde con contenido que incluye el dominio malicioso (e.g., `<script src="https://atacante.com/malicio.js"></script>`).
    
3. El atacante asegura que esta respuesta sea almacenada en la caché del CDN (quizás usando un truco para purgar la caché existente o esperando a que expire).
    
4. **Cuando los usuarios legítimos pidan la página principal de `victima.com`, el CDN les servirá la respuesta envenenada con el script malicioso**, ejecutándose en sus navegadores 210.
    

**Para practicar:** La **Web Security Academy de PortSwigger** tiene una sección excelente y muy práctica con laboratorios detallados sobre envenenamiento de caché 2. La extensión **Param Miner** para Burp Suite es invaluable para automatizar la búsqueda de cabeceras "unkeyed" como la del ejemplo 2.

#### ⚠️ **Impacto Real**

El impacto es amplificado porque un solo payload envenenado puede afectar a miles de usuarios. Se puede usar para realizar **ataques de XSS a gran escala, redireccionamientos phishing, o incluso Denegación de Servicio (DoS)** si se cachea una respuesta de error 210.

#### 🛡️ **¿Cómo Prevenirlo?**

- **Validar y Sanitizar Cabeceras HTTP:** No confiar en cabeceras como `X-Forwarded-Host`, `User-Agent`, o `Accept-Language` sin sanitizarlas apropiadamente antes de usarlas en la respuesta.
    
- **Configurar Correctamente las Claves del Caché (Cache Keys):** Asegurarse de que el caché incluya en su clave de almacenamiento todas las cabeceras y parámetros que afectan a la respuesta. Si una cabecera no se usa para generar la respuesta, debe estar "unkeyed"; si se usa, debe estar "keyed" 210.
    
- **Auditar la Configuración del Caché:** Limitar qué recursos se cachean y asegurarse de que las respuestas a métodos HTTP como POST no se cacheen.

---
#### Referencias

