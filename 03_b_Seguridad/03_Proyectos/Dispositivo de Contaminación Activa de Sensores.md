tipo::
tags::
fecha:: 2026-01-08
links::
links_externos::

Como laboratorio para las pruebas de trazabilidad de un dispositivo con GPS construiremos una "caja" que impida la lectura de la ubicación de un dispositivo tanto por GPS o por reconstrucción de la ruta guardada a través de datos de software que utilicen el giroscopio y/o el acelerómetro del dispositivo para "re-configurar" el viaje.

Para esto necesitamos primero el bloqueo de señales. Esto lo lograremos con una caja o funda faraday. Donde el dispositivo se configurará en modo avión y estará por lo menos 2 minutos fuera, puede ser apagado tambien, antes de entrar en la caja. Podríamos agregar tambien varios **imanes de neodimio pequeños y fuertes** pegados de forma desordenada en el interior o en un compartimento en esquinas sueltos de tal manera que vayan moviéndose. Así impedirémos que ande el megnetómetro que posibilita medir la relación norte sur a través de los campos magnéticos de la tierra.

Para engañar al acelerómetro y el giróscopio lo que haremos es "meter ruido" en su recopilación de datos. Para eso nos vamos a valer de un motor eléctrico chico, conectado a un plato giratorio que estará a su vez conectado a un dispositivo vibrador. Podríamos llegar a automatizar este proceso pero para una prueba piloto haremos que ambas cosas vayan conectadas a una batería y que giren y vibren de manera permanente. 

## Prototipo

**Dispositivo de Contaminación Activa de Sensores (DCAS)**

1. **Capa 1 (Fondo):** Plato giratorio 240° con rótula para movimiento libre, continuación de inercia para alterar giroscopio. 
	    [Soporte de cabeza esférica](https://articulo.mercadolibre.com.ar/MLA-1519857909-soporte-de-cabeza-esferica-m3-2-x-10-unidades-para-juntas-_JM#polycard_client=search-nordic&search_layout=grid&position=17&type=item&tracking_id=a8dc934d-aade-4859-9238-a7472f9bd48b) 10 x 26.500 => 2650 c/u
2. **Capa 2 (Contaminación Pasiva):** Imanes en módulos dentro de la caja para generar campos magnéticos aleatóreos
		[Imanes de 3 mm](https://articulo.mercadolibre.com.ar/MLA-1393209367-10-imanes-de-neodimio-8-x-3-mm-alta-potencia-redondo-_JM#polycard_client=search-nordic&search_layout=grid&position=22&type=item&tracking_id=d94f464d-8c31-492c-b1ee-62e425395fc0&wid=MLA1393209367&sid=search) 10 x 5200 => 520 c/u = 2.600
		[Imanes de 1 mm](https://www.mercadolibre.com.ar/imanes-neodimio-redondos-5x1-mm-alta-adherencia-x-20-unid/up/MLAU2957268350#polycard_client=search-nordic&search_layout=grid&position=23&type=product&tracking_id=354b4d27-9dbe-4b07-bc9b-d13291c6725c&wid=MLA1471164735&sid=search) 20 x 5300 => 265 c/u = 2.650
		[Imanes de 7 mm](https://articulo.mercadolibre.com.ar/MLA-1393933089-imanes-neodimio-redondos-7-mm-alta-adherencia-x-100-unid-_JM#polycard_client=search-nordic&search_layout=grid&position=24&type=item&tracking_id=83877929-d09d-4c69-a3b8-d37e219388c1&wid=MLA1393933089&sid=search) 100 x 14.000 => 140 c/u = 1.120
3. **Capa 3 (Aislamiento):** La **bolsa Faraday** con el teléfono, colocada dentro de la caja DCAS, sobre los motores.
	    [Funda faraday](https://www.ebay.com/itm/275286935152?_skw=funda+faraday&itmmeta=01KEHW5T08WX0EWZVM857JMZXW&hash=item4018614670:g:5AEAAOSwh1Ji9fnQ&itmprp=enc%3AAQAKAAAA8FkggFvd1GGDu0w3yXCmi1dQzfoCqibGF2t%2FFXZ%2F5gV3q4ta4r5g8BU4Nbj%2FCU7LlgtF%2Beb0S5KJkm78YoSy%2Bf%2FpBEDWs157vlhxSStEvB4ZT3pjuqVFWepSPLu32QkCsoT8QoFmPVkXAMaw3t1aL0YcZypP4fZdFFkyT8vj%2BG5%2Bro6tipRalHaNbegLAi4XQ%2F2%2FDOIhAIKqe6wyilFCg9pvMd%2FPFd3H52zuv%2Bu0kx%2Bi%2FBcAgl7MCmOBRyue8aB9CCE8xFOVTT2g2%2BalW85kqsua5b%2BTvYcds7gTVPWv9rQd2cWNBd004mN6miLhOL25YA%3D%3D%7Ctkp%3ABk9SR56gl7z0Zg) 1 x 10 USD (15.000) - 24.200
4. **Capa 4 (Cintaminación Activa):** Vibradores programables en el plato de la rótula. Conseguir de celulares viejos.
	   [Vibrador](https://www.mercadolibre.com.ar/motor-vibrador-celular-redondo-10mm-x-25mm-3v-arduino/p/MLA2048379864#polycard_client=search-nordic&search_layout=grid&position=3&type=product&tracking_id=c568fa94-8eda-481e-b45e-5904ad3d8706&wid=MLA824891713&sid=search) 1 x 4.800
	   [Arduino](https://www.ebay.com/itm/326871591676?_skw=Arduino+Pro+Mini+3.3V+8MHz&itmmeta=01KEHWGFTWKMY5816P3ZGGGE1D&hash=item4c1b10aafc:g:OCkAAeSwnWdpHLtn&itmprp=enc%3AAQAKAAAA8FkggFvd1GGDu0w3yXCmi1ejlPWagYYOLVBoTTPwxEm6i9YejjpypzOycr3p9SVjHBrhxpOmpeDC46ZnUXFXJ4mhcMTn7vyX%2FSqGyj%2B%2FEg%2BCi4cfpKeeDeO%2FGx9xN00qdIPyfBF2%2F55ab42LcBHIKNtpbsWBSZ0%2FXr9eqhnbedj8bCQT29rIyyxeAvDMdWwQtziRav%2Bzp9O698PRHOZ%2FNCt0vRXIcdyyVdsx6eQywrkqtmj8jxGZSiAusZBVzM62D90ntF9sgmPl7o7svxIGBZ2MC7PpbzlmKK3r8hQX7dDH5hEsCvXT0pCl9fiqnodueQ%3D%3D%7Ctkp%3ABFBMzP3BvPRm) 5 x 56 USD => 5,6 /u - 9.000
5. **Capa 5 (Alimentacion):** El dispositivo tiene que tener una fuente de energía para el módulo programable
		[Pilas AA](https://www.mercadolibre.com.ar/10-x-pilas-alcalinas-aa-energizer-max-e91-blister-san-martin/up/MLAU208265177?pdp_filters=shipping:fulfillment#intervention_type=full&backend_model=search-backend&position=2&search_layout=grid&type=cart_intervention&tracking_id=cfeff287-3a66-446e-a803-882ba37b2aee) 10 x 12.000 => 1.200 c/u - 4.800

**PRESUPUESTO TOTAL = $ 42.800**

![[Pasted image 20260109133300.png]]

![[Pasted image 20260109144545.png]]

Construir una funda de faraday con aluminio.  Con aluminio comprobamos que con tres capas, sin cinta (si queremos usar cinta debe ser cinta conductiva) verificamos que corta la señal del dispositivo. Para probarlo pusimos un video youtube, reproducimos hasta que en un momento no pudo descargar más datos y perdió la señal (se muteo). Esto nos asegura de que esta andando. Esta prueba se hace siempre antes de usarla. Una vez verificado esto se pone la configuración de software en modo avión o apagado y se lo pone en la funda.

Conseguir celulares viejos para extraer el dispositivo vibrador de ellos. Conseguir otros vibradores más grandes y armar con eso dos intensidades de vibración. 

1. **Microcontrolador:** ESP32-WROOM-32 (tiene Bluetooth/Wi-Fi, pero los desactivaremos por software).
2. **Sensor de Vibración:** Módulo SW-420 (con detección por umbral) o, mejor, un **Acelerómetro MEMS (como el MPU-6050)** para capturar vibraciones de manera más analógica y precisa en dB/g.
3. **Actuadores:** 2 motores de vibración (discos tipo "pancake") de diferentes potencias (ej: 3v y 5v). O mejor, **un motor de vibración lineal (LRA)** controlado por PWM, que permite variar la intensidad de forma más fina que un motor de disco simple.
4. **Fuente de Poder:** Una powerbank pequeña o batería LiPo con regulador de voltaje.
5. **Contenedor:** Una caja rígida pequeña (de plástico o aluminio) donde irá la bolsa Faraday con el teléfono. Los motores vibradores deben ir **pegados firmemente a las paredes internas** de esta caja.
6. 1. **Imanes de Neodimio** (sueltos dentro de un compartimento): ¡No olvides el contaminador del magnetómetro!
