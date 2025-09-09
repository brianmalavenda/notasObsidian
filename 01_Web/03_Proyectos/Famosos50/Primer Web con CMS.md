tipo::
tags::
fecha:: 2025-09-01
links::
links_externos::

## Sitio https://famosos50.com.ar

![[home_famosos50.png]]

Este sitio web a crear es un E-Commerce, por lo tanto lo primero que debíamos hacer es planificar con que herramienta íbamos a trabajar para poder tener de piso las funcionalidades de:

* Cargar producto
* Controlar stock
* Recibir pagos
* Controlar estado de compra

Lo primero que hicimos fue contratar un servicio de [[Hostings]], llamado Bluehost, donde desde allí podamos crear el sitio Wordpress y configurar sus características de base. Una vez que creamos el sitio base y elegimos un [[Tema y Plantilla]] para la web, trabajamos con la identidad de la marca. 

Para esto creamos un [[Manual de Marca]] con los colores, diseño de logo y tipografía de la empresa.

![[manual de marca del sitio Famosos50.png]]

Con esto pudimos combinar los colores, agregar el logo al sitio, definir los estilos de fuente. Ahora, continuamos con la idea de destino del sitio, que tipo de web es. Habíamos dicho que era una tienda virtual, bueno. Para crear una tienda virtual  instalamos un plugin de WP llamado [[Woocommerce]] que tiene de manera nativa estas funcionalidades.
Con este plugin [[Crear un producto nuevo]] luego incorporamos la  [[Integración Mercado Pago]] para el pago de los productos que cargamos. Dentro de este plugin el control de stock y el estado de compra ya están incorporados

Para realizar una [[Carga Masiva de producto]] instalamos un plugin que nos permite cargar directamente desde un csv. ![[dataset-libros-los50famosos.csv]]
Al momento de venderse y reponer el stock, sirve poder administrarlos desde una tabla y no desde navegar por cada uno. Hubo que considerar que tambien a este seguimiento de stock donde se configuró para dar una alerta cuando haya menos de 2 unidades de algún producto, tambien hay que hacer el seguimiento de la venta y el estado del envío.
Este seguimiento de la venta inicia con la [[Notificaciones de compra]] para el cliente y el administrador del sitio. Personalizamos el mail con una factura de compra para que quede registrado y luego esto queda almacenado en el sitio para definir el método de envío. El [[Estado de la venta]] se sigue desde un panel de "pedidos" donde se finalizará cuando el cliente y/o el canilla de la zona haya/n confirmado que el pedido fue entragado.

Además era requerido que la página tenga roles diferenciados de usuarios y que a roles distintos se vean por producto, distinto precio. Por lo tanto se agregó la posibilidad de hacer [[Login]] en la web. Además, incorporamos un plugin para hacer [[Segmentacion de precios]], para que depende que usuario este logueado, se muestre un precio u otro de cada producto. 

![[login_en_wordpress.png]]

Visualmente, era necesario para la página de inicio respetar la idea de utilizar un [[Carrusel de imagenes]] para los productos más nuevos mostrarlos al inicio junto a otras novedades. 

Además incorporamos un [[Newsletter]] para que los usuarios que quieran recibir novedades de la web puedan suscribirse y dejar datos que le permitan al cliente personalizar las campañas de "marketing digital". 

Desde la mirada de las funcionalidades del cliente hacia el usuario practicamente habíamos terminado con estas caracteristicas. Dentro de la administración propia del sitio, agregamos la posibilidad de [[Crear backups del sitio]].

Luego tocaba disponibilizarlos en internet para que se vuelva accesible. Para eso hubo que [[Registrar dominio]] y luego [[Delegar dominio]] para que ese dominio del cliente apuntase a los DNS del servidor donde alojamos el sitio.
mantenerlo seguro a ataques con reglas de seguridad en [[01_Web/02_Fuentes de conocimiento/Blog desarrolladores/Cloudflare]], además de configurar una CDN para servir archivos estáticos más rápido. 

```
```



---
#### Referencias

