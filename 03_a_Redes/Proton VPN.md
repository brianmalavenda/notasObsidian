tipo:: [[Seguridad Red]]
tags:: #seguridad/red/vpn #seguridad/web/vpn 
fecha:: 2025-09-06
links:: [[Que es una VPN]] [[03_b_Seguridad/02_Fuente de conocimiento/VPN]] [[Cambiar DNS]] 
links_externos::

![[Que es una VPN]]

### Enfoque en Proton VPN:

**Proton VPN** es un servicio desarrollado por **Proton AG**, una empresa suiza con un historial focalizado en privacidad (creadores de Proton Mail). Sus características técnicas relevantes incluyen:

1. **Auditorías independientes**:    
    - Su política de no registros ha sido verificada por auditorías externas (ej. Securitum, 2022), confirmando que no retienen datos de usuarios.
2. **Tecnología Secure Core**:
    - Enruta el tráfico a través de servidores en países con alta protección legal (Suiza, Islandia) antes de salir a internet, añadiendo una capa extra contra ataques de correlación de tráfico.
3. **Cifrado avanzado y protocolos**:
    - Utiliza WireGuard (optimizado para velocidad y seguridad) y OpenVPN con cifrado AES-256, estándar militar.
4. **Características contra vigilancia**:
    - **Interruptor de emergencia (Kill Switch)**: Bloquea todo el tráfico si la conexión VPN falla, evitando exposiciones accidentales.
    - **DNS privado y resistente a DNS Hijacking**: Sus servidores DNS propios evitan manipulaciones externas.
---

### ¿Por qué usar Proton VPN para privacidad?

- **Evita el rastreo por parte de ISP**: Tu proveedor de internet solo verá tráfico cifrado hacia el servidor VPN, no tu actividad real.
- **Protección en redes públicas**: El cifrado neutraliza riesgos de sniffing en WiFi abiertos.
- **Acceso anónimo a servicios**: Al ocultar tu IP, reduces la posibilidad de que servicios web te perfilen basados en ubicación o historial.
    

---

### Limitaciones a considerar:

- Una VPN **no garantiza anonimato total** si otros métodos de tracking (cookies, huella digital del navegador) están presentes.    
- La velocidad puede verse afectada por la sobrecarga del cifrado y la distancia al servidor.
- La elección del servidor VPN es crítica: servidores en países con legislaciones débiles de privacidad podrían comprometer tus datos.

### Descargar Proton VPN

Una vez creado una cuenta de Proton - desde el archivo [[Crear cuenta proton]]. Desde ProtonEmail vamos a la bandeja de entrada del email, donde siempre iniciaremos sesion. Iremos a la barra lateral derecha y tocaremos en el ícono que hace referencia a Proton VPN (adjunto imagen).

![[cuenta_prueba_5.png]]


Aparecerá la opción para descargar Proton VPN a la pc. Tocaremos ahi y esperaremos que se termine de instalar

![[crear_cuenta_6.png]]


Una vez que descargó el archivo .exe, darle doble click para que lo instale. La instalación es sensilla, se selecciona el idioma y todo "siguiente", "siguiente". Cuando nos aparece en un momento para descargar más aplicaciones de proton, destildaremos todas y también la última que hace refedencia mas al pedir de cada uno de nosotros más histórico.

Luego, abrir la aplicación. Aparecerá para logearse. Nos logeamos y en la página de inicio aparece nuestra ubicación geográfica, datos de nuestra red. Aparece el botón "Conectar" que ofrece los puntos de VPN más rapido de navegación. por cercanía o por velocidad de fibra o otra tecnología.

![[cuenta_prueba_6.png]]

![[cuenta_proton_7.png]]

Si el servidor que nos toca no nos gusta por algún motivo, nos aparecerá la opción "Cambiar servidor". Esto se define automaticamente en que servidor caigo, el plan gratuito no tiene la posibilidad de seleccionar manualmente desde que servidor quiero salir. Abajo a la derecha de la barra de tareas (la barra de abajo de la pantalla), aparecerá el ícono de proton.pass con un candado en verde, demostrado que estamos conectados. Aparecerá rojo cuando estemos desconectados. Haciendo click y apretando en "Desconectar" se puede facilmente cambiar de un estado a otro.

![[proton_barra_tareas_01.png]]
