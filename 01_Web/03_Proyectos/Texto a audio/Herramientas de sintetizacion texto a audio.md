tipo:: #web/proyectos
tags:: #ia #texto-audio 
fecha:: 2025-09-05
links:: 
links_externos::

El audio que actualmente generamos con la herramienta @voice tiene alrededor de una duración de 1 hora. Los domingos y lunes esto es un poco mayor ya que tenemos las editoriales.

Un audio de 1 hora contiene entre **120.000 y 150.000 caracteres** (asumiendo una velocidad de habla promedio de 200-250 palabras/minuto y 5-6 caracteres por palabra). Es decir, a 1,5 horas, estamos hablando de entre **180.000 y 225.000 caracteres**. 

Una edición del ejes que dura alrededor de 1 hora de audio contiene entre **65.000 y 75.000 caracteres** o 12.000 palabras de promedio (teniendo en cuenta a las ediciones con editoriales de domingo y lunes). Como hacemos audio todos los días, esto en un total mensual tomando como promedio 30 días estaríamos en:

	
	30 audios × 70.000 caracteres => 2.1 millones de caracteres/mes
	30 audios × 12.000 palabras => 360.000 mil palabras/mes
	
	20 audios x 1hs + 10 audios x 1,5 hs => 35 hs de audio


Con esta aproximación de lo que necesitamos vamos a evaluar las herramientas disponibles, su costo y realizaremos alguna prueba

### Google Cloud

Este servicio de Google ofrece la API para trabajar con hasta 4 millones de caracteres al mes gratis. A partir de ahí empieza a cobrar. Es decir, con 2 cuentas estaríamos cubriendo las necesidades mensuales. En caso de hacerlo con una sola cuenta, tendremos que pagar unos US$ 16 para estar en 8 millones de caracteres y esta un poco olgados con los 6,75 millones que necesitamos mensualmente. [Probar](https://cloud.google.com/speech-to-text?gad_campaignid=19733931997)

### MeloTTS

Vamos a probar con una instalación local de una IA que se encargue de hacer la transcripción de texto a audio. Haremos estos pasos de prueba para verificar si es viable utilizar esta herramienta
1. Instalar y ejecutar un pequeño texto que se exporte a un archivo de audio. Verificaremos el tamaño del archivo generado, los formatos posibles y la calidad
2. Generaremos el audio a partir de un archivo de texto un poco más largo. Con 400 caracteres, masomenos una nota del ejes. Veremos los resultados.
3. Enviaremos el archivo de una región, solo texto, sin imágenes. 

Vamos a comenzar descargando el repositorio desde
https://github.com/myshell-ai/MeloTTS
Este es otro, lo probaremos en segundo momento
https://github.com/inferless/melo-tts

Aca esta el instructivo para instalar y ejecutar la app: https://github.com/myshell-ai/MeloTTS/blob/main/docs/install.md

## USABILIDAD GRATUITA
https://www.youtube.com/watch?v=IT7RVsgpth4

## Avanzamos con Google API Text-to-Speech

Instalamos el CLI de google cloud en mi linux de WSL. Despues nos conectamos con google y a través de línea de comando conseguimos el auth0 2.0 Con esto conseguimos que en un script de pithon podamos usar la API de text-to-speech y traducir texto. Luego probamos en sintetizar texto en formato ssml.
Tambien, en paralelo, probamos un script en python para extraer texto de archivo word que tengan subrayado amarillo y con esto generar un nuevo texto formato .docx. 

Empezamos a pensar como es el diseño de la arquitectura para poder traducir textos que sean largos. Evaluamos escenarios con microservicios con docker y un orquestador como kubernetes. Hubo planteado otra opción, monolítica, para crear threads y poder hacerlo sin recurrir a docker ni microservicios.

## INVESTIGAR HUAWEI, ALIBABA Y BAIDU CLOUD PARA VER SI TIENEN ALGUN SERVICIO SIMILAR

---
#### Referencias

[Precios Vidnoz](https://es.vidnoz.com/api-pricing.html)
[Precios Eleven Labs](https://elevenlabs.io/es/pricing#pricing-table)
[Precios GoogleCloud](https://cloud.google.com/products/calculator?dl=CjhDaVF5T0dJMU9HRTBZaTAzWmpVMUxUUmpZekl0WVRNeE1DMDFabVF6WVRGak56RTJNVFFRQVE9PRAUGiRCQkExQ0Q5NS0yODIyLTRCMDYtQUUyRC0yQUFFNTU2NjcwRjM)