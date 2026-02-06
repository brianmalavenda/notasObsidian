tipo:: [[02_Informes cuatrimestrales]] 
tags:: #ia #seguridad #desarrollo_front #desarrollo_back #data #mpg 
fecha:: 2025-11-01
links::
links_externos::

## 0-MPG

1. Incorporamos al seguimiento de los portales informes un detalle por las empresas mas relevantes de China y EE.UU, alguna de Inglaterra y Holanda por la relevancia mundial
	1. China: Huawei, SMIC, TSMC(Taiwan), Tencent, HeSai, Bitedance, Alibaba, Baidu, Xiaomi
	2. EE.UU: Nvidia, Amazon, Global Foundries, Intel, AMD, Broadcom, Cloudflare, Google, Microsoft, OpenAI, Oracle, Salesforce, Starlink, Qualcomm, Apple
	3. Holanda: ASML
	4. Inglaterra: ARM Holdings
2. Incorporamos seguimiento de empresas argentinas
	1. Biotecnología: Biodiseños S.A
	2. Blockchain: Koinbanx, Lemon, QuarkID, Signatura, Ripio, Satoshi Tango
	3. Ciberseguridad: Auth0, Techforce, Yubico
	4. Datacenter: Datacenter TDF, Nubecenter, 
	5. Fintech: Mercadolibre, Ualá, Binance, CARF, Labitconf, 
	6. I+D: INVAP, CONICET
	7. IA: Nubimetrics(mp), NaranjaX (uala), RockingData
	8. Infraestructura: Air Computers, BANGHO, BGH Tech Partner, HDC Discos, Nodra, Novatech, PC Arts, Pixart Argentina, Unitec Blue
	9. ISP: ARSAR, CABASE, Telecom
	10. Redes: Koc Latinoamerica, BCD, Mindtec, Pulse Networks
	11. Web: Globant
3. Incorporamos el seguimiento de cámaras empresariales
	1. Cámara de la Industria Argentina del Software
	2. Cámara Argentina de Distribuidores Mayoristas de Informática, Productores y Afines (CADMIPyA)
	3. Fintech: Cámara Argentina Fintech
	4. ISP: Cámara Argentina de la Banda Ancha(CABASE)

### Informe

Tenemos 14 portales de mundo + 4 de argentina + 4 cámaras empresariales + 17 institutos de investigación tanto educativos como gubernamentales. Es decir, en total son 39 (40) portales a seguir diariamente. 

Producto del anuncio del proyecto de Stargate en Argentina con la construccion del datacenter en la patagonia elaboramos un informe (primer informe de MPG)

## 1- Infraestructura

### Servidor

Armamos una notebook vieja como servidor de pruebas. Le instalamos un sistema operativo Open Media Vault. Programamos backups del disco, virtualizamos aplicaciones y pusimos en producción (hacer accesible desde afuera de la red local) aplicaciones como la Nube Andes y una aplicacion de pruebas. 

![[servidor sm901 - pc.png]]

![[servidor sm901 - omv dashboard.png]]

Investigamos y armamos el listado de los recursos necesarios para construir un servidor casero con posibilidad de llegar hasta 200 usuarios que utilizaremos como nuestro datacenter con una nube propia, plataforma de streaming de video y servidor para hospedar páginas web's.

Manejamos un presupuesto de 3 millones cotizado a fin de octubre con un dolar 1420. Al momento del cierre del informe el dolar esta a 1475.
Al presupuesto tenemos que tener en cuenta herramientas para la instalación y mantenimiento.

### Compras 
#### Internacionales

Placa madre con chipset X99 Huananzhi  - AliExpress
Procesador Intel Xeon E5 V4 - AliExpress
Memoria RAM 16 GB x4 - AliExpress // este combo son US$ 322 + 123
Disco SSD tipo NVME 1 TB x2 - AliExpress US$ 115X2 + 49
Tarjeta gráfica NVIDIA TESLA P4 - eBay - US$ 66 - OK
Fuente Thermatake 600 W - Amazon - US$ 66 - OK
Disipador tarjeta gráfica TESLA - eBay - US$ 22 - OK 
Disipador - AliExpress - US$ 32 + 7
WAVLInk 2,5g - US$ 25 + 16
DISCOS HDD 6 TB x3 - Amazon US$ 130 x2 - OK
Módulo RJ45 SFP 10 Gbit - AliExpress US$ 10 - OK
Keeplink Conmutador - AliExpress US$ 35 - OK
Cable Ethernet Cat6 Gigabit - AliExpress US$ 25 - OK

TOTAL = US$ 1093

TOTAL impuestos + costo de envío acumulado = US$ 123 + 49 + 7 + 
TOTAL = US$ 1597 ($ 2.355.575) actualmente

#### Nacionales

UPS Enertik AL1500  - enertik $445.000 [pedido](https://enertik.com/ar/finalizar/order-received/48929/?key=wc_order_n4mvMqJQFXQQP&collection_id=140031269728&collection_status=approved&payment_id=140031269728&status=approved&external_reference=WOO-48929&payment_type=account_money&merchant_order_id=36848324175&preference_id=204237-7e5449fd-227e-455e-af4a-2fb95db0f0d3&site_id=MLA&processing_mode=aggregator&merchant_account_id=null) - OK
Gabinete Gamer Corsair 3000d Rgb Airflow 3x120mm Argb Fans Negro - $ 200.000
Ventiladores - 8.000 x 5 = $ 48.000

TOTAL = $ 

Herramientas de mantenimiento = 
1. Soplador eléctrico = 32.000

### ver más adelante

Gateway de seguridad

Mini PC [ebay](https://www.ebay.com/itm/186916871768?_skw=mini+pc&itmmeta=01KDQFXXGN62R2BRY62R0S1QTT&hash=item2b851d1e58:g:8ScAAOSwyEtnmOFh&itmprp=enc%3AAQAKAAAA4FkggFvd1GGDu0w3yXCmi1dq534p0QeJTdjVQ8bjOOxWRwKo%2F8MDBV02odIGfmowGqmxEM77QnEhrOcFdrd%2Fec8Z8pVAlQUOxiPNbi220e0KtzXk%2BncHLk8y6OKn4ukADBxIaejVLTcP4ibYtxaqsCZiE38AYTMv6Lfkh8PBxtiWXuo4VJ0nBAOrlh1%2BTqXTcCYXcbD3Aei58X1pWGwip6WEJLXVQ8qaLmb%2FyaoG%2FJnUg38ckUw9KqqybPe4t%2FXrcUjdH4QGPLAGsNb1NBX4VO0SAVX2qViu1QlWvF37k9mM%7Ctkp%3ABk9SR9rY9-_tZg) US$ 160 = $ 225.000
Mini PC [ebay](https://www.ebay.com/itm/365480551655?_skw=mini+pc&itmmeta=01KDQFXXGNYNBG2ZBEQJ61QX0D&hash=item551856a4e7%3Ag%3Ab6QAAOSw1u9n4cHe&itmprp=enc%3AAQAKAAAA4FkggFvd1GGDu0w3yXCmi1fdJ4qseOYua2ebJXrsmhvVZWG79If5cibXJDBUhajilNr1uF42rZcKW23798Sw%2FTX4tBLun2qSe5dNN6j9pvOjK13wvxvDvmJEtFAUKjrCDSwwx2S8EAv%2BqhWW%2BjXKPTgbmlQhTORPMp1KfQPU4qCOt5FAGHAeOY8wA9UaS2xCl6krx0qdZ8o%2FuIzLRB17DlICJxV4fuKbI8lkxM9vfhV65wkpVbCSAMeQngworWzljlyVKH92sEBLxg8Zae9fco%2BxdUlm0ho0V9W24u%2BtH8bu%7Ctkp%3ABk9SR9rY9-_tZg&var=635286807808) US$ 110 = $ 170.000
### Mantenimiento

Hicimos la revisión y puesta a punto de 5 notebooks.

1. JDM-ENOVA: instalación de Windows, programas base, desinstalacion de aplicaciones de telemetría de windows
2. SSB-EXOSMART: hay teclas que no andaban. Desarmamos, verificamos sulfatamiento del conector del teclado con la placa, no era eso. Verificamos error de software, no encontramos problemas. Hay que cambiar el teclado.
3. SO-LENOVO: formateo para ver si mejoraba el rendimiento, estaba muy lenta. Los componentes ya estaban viejos, 2 GB de RAM con la memoria soldada a la placa, no se puede cambiar o ampliar. Intentamos con distitnas distribuciones de sistemas operativos basados en linux. Quedo instalado lubuntu, sirve para tareas básicas.
4. VS-LENOVO: la compu no encendía. Cambiamos la pila de la placa CR2032 por una nueva, encontramos el fallo en la batería. Reseteamos error en la placa y ok. Queda comprar repuestos.
5. JFV-HP: formateo, revisión porque continuaba lenta, vimos componentes incompatibles de base, cambiamos discos almacenamiento mecánico por ssd y compramos una RAM de 4 gb DDR4 para ampliar la ya instalada.

## 2- Nube

Utilizamos código abierto base de una nube tipo Nextcloud que adaptamos para usarla nosotros. La instalamos en nuestro servidor casero de pruebas, realizamos configuraciones para acceder desde fuera de la red local y probamos acceder con varios usuarios. Probamos guardar y descargar archivos. Subimos distintos tipos de archivo sin problemas. 
Estas pruebas fueron con una funcionalidad básica que adaptamos a los recursos del server, para la instalación productiva sumaremos otras funcionalidades como la de escritura colaborativa en documentos compartidos.

![[los andes cloud - compartir.png]]

![[los andes cloud - archivos.png]]

## 3- Web

Desarrollamos una aplicación para extraer el texto que resaltamos en las notas de los portales y que además convierta ese texto en audio. A su vez, desde la aplicación podremos compartir directamente a telegram para enviar el archivo del diario pintado y el audio con solo "lo pintado".

![[Pasted image 20251230093414.png]]
![[Pasted image 20251226110439.png]]