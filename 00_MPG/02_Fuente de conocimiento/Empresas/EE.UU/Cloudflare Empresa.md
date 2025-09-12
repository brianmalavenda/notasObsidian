tipo:: #mpg/empresas 
tags::
fecha:: 2025-09-08
links:: [[Certificados TLS]] , [[Fina RDC 2020]] , [[Fina Root CA]], [[Programa de certificados raíz de Microsoft]]
links_externos::

## Certificados TLS 1.1.1.1 hackeados

Las personas en los círculos de seguridad de Internet están haciendo sonar la alarma sobre la emisión de tres Certificados TLS para 1.1.1.1, un servicio DNS ampliamente utilizado de la red de distribución de contenidos Cloudflare y el registro de Internet del Centro de Información de Redes de Asia Pacífico (APNIC).
Los certificados, emitidos en mayo, se pueden utilizar para descifrar consultas de búsqueda de dominio cifradas a través de DNS sobre HTTPS o DNS sobre TLS. Ambos protocolos proporcionan cifrado de extremo a extremo cuando los dispositivos de usuario final buscan la dirección IP de un dominio particular al que desean acceder. Dos de los certificados seguían siendo válidos en el momento en que esta publicación se publicó en Ars.

Aunque los certificados fueron emitidos hace cuatro meses, su existencia no se hizo pública hasta el miércoles en un post a un foro de discusión en línea. Fueron emitidos por [[Fina RDC 2020]], una autoridad certificadora subordinada al titular del certificado raíz [[Fina Root CA]]. El Fina Root CA, a su vez, cuenta con la confianza del [[Programa de certificados raíz de Microsoft]], que rige en qué certificados confía el sistema operativo Windows.

El comunicado continuó diciendo que los datos cifrados a través de la VPN WARP de Cloudflare no se ven afectados. [[Microsoft]] dijo en un correo electrónico que “ha contratado a la autoridad certificadora para solicitar una acción inmediata. También estamos tomando medidas para bloquear los certificados afectados a través de nuestra lista de no permitidos para ayudar a mantener protegidos a los clientes” El comunicado no explicó cómo la empresa no logró identificar el certificado emitido incorrectamente durante un período de tiempo tan largo.

---
#### Referencias

