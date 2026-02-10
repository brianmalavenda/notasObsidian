
tipo:: #web/wordpress
tags:: #backups #wordpress #plugins #updraftplus #blog_desarrolladores
links: [[03_Plugins]]
links_externos::
========
Tags: #backups #wordpress #plugins #updraftplus #blog_desarrolladores
Links: [[03_Plugins]]
>>>>>>>> 20250903:01_Web/Blog desarrolladores/Wordpress/Wordpress/Crear backups del sitio.md

Acceder desde el menú a la opción "Plugins" para descargar uno llamado UpdraftPlus.

![[plugin_wordpress_menu_1.png]]

Desde la página de plugins, tocar en el botón "Añadir plugin" o "Add plugin". Luego allí mismo en el menu ir a la opción "Feature" y luego en el buscador poner el nombre del plugin que queremos instalar para hacer el back up, "UpdraftPlus".

![[menu_añadir_plugin_1.png]]

Aparecerá la aplicación con la siguiente imagen. Darle en instalar y luego activar.

![[updraftplus.png]]

Como todos los plugins, este nuevo aparecerá en el menú lateral izquierdo. Acceder a el y en la página principal, bien grande aparecerá un botón de Backup now con el cual rapidamente se podrá hacer un almacenamiento del sitio tal cual esta actualmente.

![[updraftplus_inicio_1.png]]

Apretando el botón aparecerá una ventana de configuración de la copia. Si no tienes un lugar externo al sitio donde guardar el backup debe destildar la tercer opción "Send this backup to rmeote storage".

![[updraftplus_configuracion_1.png]]

Abajo aparecerán todos los backups hechos. Es recomentable ir borrando los más viejos para no desperdiciar espacio de almacenamiento. Tocando en el botón "Others" tenemos la posibilidad de descargar la copia de seguridad a nuestra computadora para tener un doble respaldo de la última copia.

![[updraftplus_backup_listado.png]]

### Descargar directamente en computadora personal

Para que la copia de seguridad no se guarde en el servidor de hosting, se debe configurar una conexion FTP y crear copias automáticas en un periodo de tiempo definido de acuerdo a los cambios que se hagan en la web, además de la frecuencia de cambios desde los administradores tambien hay que tener en cuenta los cambios que se generan por los usuarios clientes.


#### Configurar desde un cliente de FTP instalado en la computadora personal

Creando un usuario en el servidor de FTP que es el servidor hosting, a través de la aplicación Filezilla accedemos a la carpeta del servidor donde tenemos los backups. 
Para esto debemos crear previamente una carpeta en publich_html que es el root del sitio web que podemos llamar "backUp". 
Con los datos del servidor, usuario y contraseña, (sin necesidad de poner el puerto). Ya podremos acceder al servidor desde la computadora personal vía FTP con protocolo de seguridad TLS.

![[backup_ftp_1.png]]

A la derecha aparece la carpeta del servidor y a la izquierda las carpetas de nuestra computadora. Es tan simple como arrastrar aquellos archivos zip de los backups que queremos almacenar en nuestra PC.

Desde el servidor vamos al sitios que queremos hacer la copia de seguridad y aregamos una cuenta FTP.

![[backup_updraftplus_03.png]]
En este caso es la cuenta [mail@[dominio]] . Despues vamos al sitios y a la configuracion del plugin. Allí configuramos el plugin para que el backup se hace en una carpeta conectada a FTP y abajo configuramos la conexión con ese usuario del servidor donde se aloja el sitio en este caso.

#### Configurar desde un servidor de FTP instalado en la computadora personal

Lo dejamos por aca https://www.inmotionhosting.com/support/edu/wordpress/plugins/ftp-backup/


## Backup del sitio sin instalar aplicaciones

Sin ninguna configuración, así como esta configurado el plugin
![[backup_updraftplus_02.png]]

El backup se almacena directamente en la carpeta public_html del sitio.

![[backup_updraftplus_01.png]]

