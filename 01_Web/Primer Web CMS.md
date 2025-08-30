
tipo::
tags::
fecha:: 2025-08-29
links::
links_externos::

Hicimos una aplicación para la Distribuidora donde llegamos hasta presentar el primer producto viable (MVP) para que ponga a prueba el cliente, en este caso, la distribuidora y con eso poder terminar de retocar parte del diseño y alguna cuestión técnica del funcionamiento adaptado a la usabilidad de la empresa.

![[home_famosos50.png]]

## Estimación de Trabajo

Arrancamos con una estimación de que una web en [[01_Wordpress]] nos llevaría:
* 1hs - Relevamiento con empresa cliente
* 56hs - Desarrollo de la Web
* 8hs - Desplegar el [[MVP - Minimo Producto Viable]] para que quede funcional para el cliente y pruebas
* 16hs - Retoques de la devolución del cliente
* 8hs - Puesta en producción, [[Configurar Cloudflare (CDN)]] [[Registrar dominio]] [[Delegar dominio]]


## Sitio a Crear

Este sitio web a crear es un E-Commerce, por lo tanto lo primero que debíamos hacer es planificar con que herramienta íbamos a trabajar para poder tener de piso las funcionalidades de:

* Cargar producto
* Controlar stock
* Recibir pagos
* Controlar estado de compra

Para esto instalamos un plugin de WP llamado [[Woocommerce]] que tiene de manera nativa estas funcionalidades.
Con este plugin [[Crear un producto nuevo]] luego incorporamos la  [[Integración Mercado Pago]] para el pago de los productos que cargamos. Dentro de este plugin el control de stock y el estado de compra ya están incorporados

Para realizar una [[Carga Masiva de producto]] instalamos un plugin que nos permite cargar directamente desde un csv. ![[dataset-libros-los50famosos.csv]]


---
#### Referencias

