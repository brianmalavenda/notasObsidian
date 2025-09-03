Tags: #wordpress #seguridad
Link: [Yotube - 1er momento de inspeccion vulnerabilidades](https://www.youtube.com/watch?v=LrTt2RKLT1E)
Herramientas: Kali Linux, WPScan
Link: [Yotube - Cómo PROTEGER mi Página Web de Hackers](https://www.youtube.com/watch?v=xUc2DVnrfio)
Herramientas: Kali Linux, WPScan

1- Version de Wordpress actualizada
2- Ocultar path de login /wp-admin
3- Certificado SSL
4- Bloqueo de países
5- Contraseñas: Mientras más caracteres mejor
6- Publicacion spam por comentarios
7- Probar que el sitio wordpress esta con autenticacion HTTP
8- Usar alias para los usuarios que no tengan los nombres de alias directamente
9- Bloqueo de inicio de sesion para ataques de fuerza bruta
10- Lista blanca de IP's
11- Lista negra de IP's
12- Verificar permisos sobre recursos/archivos del servidor
13- Borrar archivos "basura" en instalacion de core
14- Deshabilitar opcion de click derecho, seleccionar texto, copiar
15- Bloqueear mostrar mi web en un iframe por otro sitio web
16- Uso de los feeds RSS y ATOM para leer contenido de sitio
17- `xmlrpc.php` es una vulnerabilidad
18-  Bloquear debug.log
19- Firewall
20- Cambio URL login
#### 5- Contraseñas
Market Hax, para ponen a prueba tu contraseña
#### ¿Que es un salt?
Un salt es una cadena aleatoria que se agrega al valor original (por ejemplo, una contraseña) antes de hashearla. El objetivo es:
* Evitar ataques por diccionario o con tablas precomputadas (como Rainbow Tables).
* Asegurar que dos usuarios con la misma contraseña no tengan el mismo hash en la base de datos.
#### 8- Nombre de usuario
En la distribuidora es la inicial nombre, apellido y el sufijo lf50

#### 12- Permisos sobre recursos

* wp-includes/	
* .htaccess	
* wp-admin/index.php	
* wp-admin/js/	
* wp-content/temas/	
* wp-content/plugins/	
* wp-admin/	
* wp-content/	
* wp-config.php

#### 13- Eliminar archivos basura del core

Eliminar readme.html, license.txt y wp-config-sample.php
#### 14- Deshabilitar opcion de click derecho, seleccionar texto, copiar

Deshabilitar las opciones "Clic derecho", "Selección de texto" y "Copiar" en la parte frontal de su sitio.

#### 17- `xmlrpc.php` es una vulnerabilidad
##### Ataques de fuerza bruta
Permite múltiples intentos de login en una sola petición (`system.multicall`), lo que **evade muchas medidas de seguridad**.
##### DDoS por amplificación
Un atacante puede usar xmlrpc.php para enviar una gran cantidad de solicitudes a otros sitios (como un espejo), causando sobrecarga y consumo de recursos.
##### Pingbacks maliciosos
Algunos atacantes abusan de la función de pingback para escanear otras webs o realizar ataques DDoS distribuidos.

#### 20- Cambio URL login
https://hne.ctp.mybluehost.me/ingresar una cadena que represente el slug de su página de inicio de sesión segura. Le recomendamos elegir una cadena difícil de adivinar que solo usted pueda recordar.