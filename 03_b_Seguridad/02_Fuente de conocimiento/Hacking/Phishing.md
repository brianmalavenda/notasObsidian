
tipo:: [[Seguridad Web]] 
tags:: #seguridad/web/phising  #web/seguridad
fecha:: 2025-08-29
links::
links_externos:: [bleepingcomputer](http://bleepingcomputer.com/news/security/hackers-steal-microsoft-logins-using-legitimate-adfs-redirects/), [iplogger](https://iplogger.org/es/ )


Los piratas informáticos están utilizando una técnica novedosa que combina vínculos legítimos  _de office.com_ con Servicios de federación de Active Directory (ADFS) para redirigir a los usuarios a una página de phishing que roba inicios de sesión de Microsoft 365.

El método permite a los atacantes eludir la detección tradicional basada en URL y el proceso de autenticación multifactor aprovechando un dominio confiable en la infraestructura de Microsoft para la redirección inicial.

## Obtener información de usuario a través de web

Con esta web podemos usar un shortcut para una URL cualquiera que funcione como "sebo". La metodología es, el usuario víctima debe hacer click en la URL que https://iplogger.org/es/ ayudará a construir. Esta URL contiene la posibilidad de rastrear ubicacion del usuario, obtener metadata del dispositivo, IP de su proveedor de internet, etc. 
Es un tipo de hacking con ingeniería social porque esta implicito una relacion de confianza o de credulidad de que el enlace que le estamos enviando es seguro.
Si el usuario, además, cuando la página le pide "acceso a su ubicacion", toca instintivamente en OK y da permisos, entonces esta página obtiene toda la informacion del usuario, incluida la que proporciona el servicio GPS.

---
#### Referencias

