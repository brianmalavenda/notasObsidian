tipo::
tags::
fecha:: 2026-08-11
links::
links_externos::

Cuando se vence un certificado, el servidor contesta con un error 526. Como nosotros configuramos cloudflare como proxy, además de la validación del vencimiento del certificado se chequean otras cosas.

Lo primero es acceder a NGINX y actualizar el cerficado para cambiar la fecha de expiración.

## Renovar el certificado

Antes de entrar a NGINX, asegurarnos que, como es nuestro caso, sacar el proxy de Cloudflare y que quede solo con la función de DNS global. Una vez hecho eso, nos logueamos en NGINX que nos va a mostrar el dashboard de inicio. Ahi vamos a la pestaña de Certificados y allí buscamos el dominio que queremos actualizar.
![[Pasted image 20260811094255.png]]

Vamos a tomar un ejemplo donde tenemos un dominio pronto a vencer. Una vez que identificamos, en este caso, a collabora de nextcloud, vamos a tocar en los "tres puntitos" y ahi le damos a "Renew". Esto va a demorar uno segundo y listo.

Hecho esto, va a cambiar la fecha de caducidad del certificado y se va a ver en la grilla donde se muestran todos los dominios.

Para verificar que el cambio este hecho correctamente, vamos a ir al servidor de NGINX y ejecutamos esta línea de código:

```bash
echo | openssl s_client -connect 127.0.0.1:443 -servername andes.sanmartinici.com.ar 2>/dev/null | openssl x509 -noout -dates

### esto va a devolver la fecha anterior de caducidad y la nueva

notBefore=Aug 11 00:16:38 2026 GMT
notAfter=Nov  9 00:16:37 2026 GMT
```

Una vez hecho esto y verfiicado, puede ser que tengamos problemas con la actualización en internet de este cambio. En particular tuvimos que cambiar la rigidez con la que se valida una url válida a través de cloudflare.

Para eso fuimos a la sección SSL/TLS, "Configurar" y allí cambiamos el modo de **Completo (estricto)** a **Completo**

![[Pasted image 20260811093910.png]]

![[Pasted image 20260811095141.png]]

Una vez hecho el cambio se debería poder acceder sin problemas a la página. Volvemos a activar el proxy en claudflare para la página y consecuentemente, cuando haya pasado algunas horas podemos volver a cambiar de modo Completo a Comlpeto(estricto).

### La diferencia entre Full y Full (Strict)

Cloudflare maneja el cifrado en dos tramos: `Usuario <---> Cloudflare <---> Servidor de Origen (tu Nginx)`.

```
[ Usuario ] <---(Cifrado)---> [ Cloudflare ] <---(Cifrado)---> [ Tu Nginx NPM ]
```

- **Modo Full (Strict):** Cloudflare **exige** que el certificado de tu servidor de origen cumpla 3 condiciones estrictas:
    1. No estar vencido.
    2. Estar emitido por una Autoridad Certificadora (CA) pública/válida.
    3. Coincidir exactamente con el nombre de dominio.

_Si algo falla en tu origen (como el vencimiento que vimos recién), Cloudflare corta la conexión y te arroja el **Error 526**._
    
- **Modo Full:** Cloudflare **sigue cifrando el tráfico** entre sus servidores y tu Nginx, pero **omite la validación estricta del certificado de origen**.
    
_Es decir: no le importa si el certificado de tu Nginx venció hace unas horas, si es autosignado o si hubo un desajuste temporal al recargar los archivos. Mientras tu puerto 443 responda con encriptación, Cloudflare lo acepta y te deja pasar._
    
### ¿Es seguro dejarlo en modo Full?

**Sí, totalmente seguro para un entorno personal o homelab.** El tráfico entre el usuario exterior y tu servidor sigue estando 100% cifrado. La única diferencia es que Cloudflare confía a ciegas en el certificado que le presenta tu Nginx en lugar de auditarle la fecha de vencimiento a cada segundo.

 >[!WARNING] Puedes dejarlo en modo **Full** para no volver a quedarte fuera si un certificado de Let's Encrypt tarda en renovarse en el futuro. Si más adelante configuras la renovación por **DNS Challenge** en NPM, podrás volver a _Full (Strict)_ sin riesgos.


---
#### Referencias

