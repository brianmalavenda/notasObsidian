tipo::
tags::
fecha:: 2025-11-01
links::
links_externos::


### **El Viaje de la Petición Web: Paso a Paso**

El proceso se puede dividir en tres grandes etapas: la preparación de la red, la negociación del servidor (seguridad) y el procesamiento del navegador (renderizado).

#### **Etapa 1: La Preparación de la Red y el DNS (Dónde está la página)**

Antes de que se pida el contenido, tu navegador debe saber dónde encontrar el servidor.

1. **Análisis de la URL y Petición DNS:**
    - Tu navegador toma la dirección (ej: `https://ejemplo.com`).
    - Verifica si la ha visitado recientemente (caché local/DNS).
    - Si no, pregunta a tu router, que a su vez pregunta a un **servidor DNS** (Domain Name System). La pregunta es: "¿Cuál es la dirección IP de `ejemplo.com`?"
        
2. **Resolución de la IP:**
    - El servidor DNS devuelve la dirección IP pública del servidor web (ej: `192.0.2.1`).
        
3. **Establecimiento de la Conexión TCP:**
    - Tu navegador ahora sabe la IP y puede intentar abrir una conexión de red con el servidor. Utiliza el protocolo **TCP (Protocolo de Control de Transmisión)** para establecer un canal de comunicación fiable mediante un proceso conocido como _Three-Way Handshake_.
        
#### **Etapa 2: La Negociación de Seguridad (Certificado SSL/TLS)**

Dado que has escrito una dirección `https` (protocolo seguro), la seguridad es lo primero. **Esto ocurre ANTES de pedir o recibir el HTML.**

4. **Handshake TLS (Transport Layer Security):**
    
    - Tu navegador y el servidor inician un proceso llamado _Handshake_ para crear un canal de comunicación encriptado.
        
    - **Validación del Certificado SSL/TLS:**
        - El servidor envía su **Certificado Digital (SSL/TLS)**.
        - Tu navegador comprueba: **a)** Si el certificado es válido, **b)** Si no ha caducado, y **c)** Si fue emitido por una Autoridad Certificadora (CA) de confianza (por ejemplo, Let's Encrypt, DigiCert, etc.).
        - **Si el certificado no es válido, el navegador detiene todo el proceso y te muestra una advertencia de seguridad.**
            
    - Una vez que el certificado es validado, el navegador y el servidor acuerdan una clave de encriptación simétrica para cifrar todos los datos que se intercambiarán a partir de ese momento.
        
5. **Envío de la Petición HTTP (Encriptada):**
    
    - Solo ahora, a través del canal seguro, tu navegador envía la petición HTTP: "GET / HTTP/1.1" (es decir, dame el archivo que corresponde a la página principal).

#### **Etapa 3: El Procesamiento del Servidor y la Descarga del Contenido**

El servidor recibe la petición y responde.

6. **Procesamiento del Servidor:**
    
    - El servidor (ej. Nginx o Apache) recibe la petición.
    - Si es un sitio estático, simplemente lee y envía el archivo HTML.
    - Si es un sitio dinámico (WordPress, Django, Node.js): El servidor ejecuta código **backend** (PHP, Python, JavaScript, etc.), consulta bases de datos (SQL), genera el archivo HTML final a medida para el usuario.
        
7. **Respuesta del Servidor:**
    
    - El servidor envía el HTML generado al navegador (cifrado, por supuesto).

#### **Etapa 4: El Procesamiento y Renderizado del Navegador (La Carga de la Página)**

El navegador recibe el HTML y comienza a mostrar la página.

8. **Parsing del HTML (Estructura):**
    - El navegador lee (parsea) el código HTML línea por línea y crea el **Modelo de Objeto del Documento (DOM)**. Esto define la estructura y el contenido de la página.
    - El navegador comienza a mostrar la página, a menudo en blanco o con texto sin formato.
        
9. **Descarga de Recursos Críticos (CSS y Bloqueo):**
    
    - Cuando el navegador encuentra etiquetas `<link>` (para CSS) o `<script>` (para JavaScript), detiene temporalmente el _parsing_ del HTML.
    - **CSS:** Se descargan los archivos CSS. El navegador crea el **Modelo de Objeto CSS (CSSOM)**. **El navegador no puede empezar a renderizar la página visible hasta que el CSS esté descargado y procesado, ya que el estilo afecta la forma de la página.**
    - **JavaScript:** Si el JavaScript no tiene atributos como `async` o `defer`, el navegador también detiene el _parsing_ del HTML, **descarga el archivo JS, lo ejecuta completamente, y solo entonces continúa con el HTML restante.** Este es el famoso "render-blocking" de JavaScript.
        
10. **Construcción del Árbol de Renderizado y Pintado:**
    
    - Una vez que tiene el DOM (estructura) y el CSSOM (estilo), el navegador combina ambos para crear el **Árbol de Renderizado**.
    - Luego, realiza el **Layout** (calcula el tamaño y la posición exacta de cada elemento en la pantalla) y finalmente el **Painting** (pinta los píxeles en la pantalla). ¡La página aparece!
        
11. **Ejecución de JavaScript (Interactividad):**
    
    - Una vez que el código JS se ha descargado y se ha cargado el DOM, el código JavaScript se ejecuta. Esto añade la interactividad: maneja clics, animaciones, peticiones de datos adicionales (AJAX) y manipula el DOM para cambiar la página sin recargarla.


---
#### Referencias

