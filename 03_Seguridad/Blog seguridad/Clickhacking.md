tipo::
tags::
fecha:: 2025-08-29
links:: [[Comandos entre sitios (XSS)]] [[Envenenamiento de caché]] 
links_externos:: [bleepingcomputer](https://www.bleepingcomputer.com/news/security/major-password-managers-can-leak-logins-in-clickjacking-attacks/) 

Los actores de amenazas podrían explotar los problemas de seguridad cuando las víctimas visitan una página maliciosa o sitios web vulnerables a secuencias de [[Comandos entre sitios (XSS)]] o [[Envenenamiento de caché]], donde los atacantes superponen elementos HTML invisibles sobre la interfaz del administrador de contraseñas.

**Aunque los usuarios creen que están interactuando con elementos inofensivos en los que se puede hacer clic, en realidad activan acciones de autocompletar que filtran información confidencial.**

 Investigadores de la empresa de ciberseguridad Socket [verificaron posteriormente los hallazgos](http://socket.dev/blog/password-manager-clickjacking)
 El investigador probó su ataque en ciertas versiones de 1Password, Bitwarden, Enpass, iCloud Passwords, LastPass y LogMeOnce, y descubrió que todas sus variantes basadas en navegador podrían filtrar información confidencial en ciertos escenarios.
## Métodos de explotación

La principal mecánica de ataque es ejecutar un script en un sitio web malicioso o comprometido que utiliza configuraciones de opacidad, superposiciones o trucos de eventos de puntero para ocultar el menú desplegable de autocompletar de un administrador de contraseñas basado en navegador.

![Manipulación de la opacidad de los elementos del administrador de contraseñas](https://www.bleepstatic.com/images/news/u/1220909/2025/August/opacity.jpg)

**Manipulando la opacidad de los elementos del gestor de contraseñas**  
_Fuente: Marek Tóth_

Luego, el atacante superpone elementos intrusivos falsos (por ejemplo, banners de cookies, ventanas emergentes o CAPTCHA) para que los clics del usuario recaigan en los controles ocultos del administrador de contraseñas, lo que da como resultado que se completen los formularios con información confidencial.

Tóth demostró múltiples subtipos basados ​​en DOM que constituyen variantes de explotación de la misma falla, incluida la manipulación directa de la opacidad de elementos DOM, la manipulación de la opacidad de elementos raíz, la manipulación de la opacidad de elementos padres y la superposición parcial o total.

El investigador también demostró la posibilidad de utilizar un método donde la interfaz de usuario sigue al cursor del mouse, por lo que cualquier clic del usuario, sin importar dónde esté posicionado, activa el llenado automático de datos.

1Password categorizó el informe del investigador como “fuera de alcance/informativo”, argumentando que el clickjacking es un riesgo web general que los usuarios deberían mitigar.

Actualmente, los siguientes administradores de contraseñas, que juntos tienen alrededor de 40 millones de usuarios, son vulnerables a los métodos de ataque de Tóth

- 1Password 8.11.4.27
- Bitwarden 2025.7.0
- Enpass 6.11.6 (corrección parcial implementada en 6.11.4.2)
- Contraseñas de iCloud 3.1.25
- LastPass 4.146.3
- LogMeOnce 7.12.4

Los proveedores que implementaron correcciones son Dashlane (v6.2531.1, publicada el 1 de agosto), NordPass, ProtonPass, RoboForm y Keeper (v17.2.0, publicada en julio)
#### Referencias

