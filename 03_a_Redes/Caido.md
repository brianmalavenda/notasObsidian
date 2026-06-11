tipo::
tags:: #redes
fecha:: 2025-11-25
links::
links_externos::https://caido.io/

Para instalar la aplicación hay que descargarla de la pagina oficial. Una vez hecho eso, al abrirla la app necesita conectarse a su servidor donde nos crearemos el usuario. Yo lo cree con mi mail de google. Hecho esto, la registracion se completa y se abre la estacion de trabajo.
[El proxy de la app viene por defecto configurado en 127.0.0.1:8080]. La primer prueba la hacemos con este puerto

Creamos un proyecto (se puede hasta dos en la version gratuita) y listo. Ahora tenemos que, ene l navegador que solemos usar, yo lo configure en Brave, instalar desde el WarShop de extesniones de google la extension gratuita FoxyProxy.

Una vez añadida creamos el filtro llamado Caido con el host local y el puerto 8080. Si tratamos de acceder directamente a la pagina (nuestra en este caso) de pruebas, no va a tirar este error.

>[!ERROR]
**La conexión no es privada**
Es posible que los atacantes estén intentando robar tu información de **cloud.sanmartinici.com.ar** (por ejemplo, contraseñas, mensajes o tarjetas de crédito). [Más información sobre esta advertencia](chrome-error://chromewebdata/#)**net::ERR_CERT_AUTHORITY_INVALID**
Este servidor no ha podido probar que su dominio es **cloud.sanmartinici.com.ar**, el sistema operativo de tu ordenador no confía en su certificado de seguridad. Este problema puede deberse a una configuración incorrecta o a que un atacante haya interceptado la conexión.

Esto es porque, CAIDO actua como Man in the midle de nuestra conexión cliente con el servidor de la web a la que vamos a acceder y esto es capturado por el navegador y el sistema operativo. Para hacer que CAIDO sea autorizado a este sin meter "ruido" debemos descargarnos el certificado que valida su identidad

>[!NOTA]
>Ir al ícono de la persona en la app -> CA Certificate -> Donwload

Cuando lo descargamos primero lo "instalamos" en el navegador como indica la documentación de la app https://docs.caido.io/guides/ca_certificate_managing.html#ca-certificate-management y despues lo importamos en los certificados de confianza del sistema operativo.

>[!NOTA]
>Windows + R -> certlm.msc -> **Entidades de certificación raíz de confianza > Certificados** -> (click derecho) Importar ** seguir el wizard



---
#### Referencias

