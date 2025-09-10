
Tags: 
Links: https://www.youtube.com/watch?v=WxUmv1Mk1Nw

## REQUERIMIENTOS

Si se trata de una aplicación web destinada al uso de los usuarios a través de internet navegando por browsers desde una PC o dispositivo móvil requerirá:

### Etapa de desarrollo

[fecha: 01/07/25]

Para el desarrollo, debemos disponer de servicio a internet con banda ancha. Cualquier servicio de proveduría de internet hogareños sirve, no es necesario un plan destacado. La conexión por cable es mas segura y suele ser mas rápida que por wifi. Por lo tanto, depende de donde es el lugar de trabajo y la ubación del modem se necesitará más cable o repetidores de señal si buscamos conexión por wifi.

Lo siguientes son terminales para programar, computadoras de tipo portátiles, notebooks o de escritorio. Con un relevamiento rápido de las páginas de internet, una PC de escritorio con todos los periféricos instalados para funcionar estaría unos $720.699 con un procesador Ryzen 5 5600GT. Mientras que una notebook ASUS VIVOBOOK con un micro i5-1235U, esta unos $ 860.000, una diferencia de $140.000. Si compramos 6 terminales de escritorio estaríamos ahorrando $ 840.000. El gasto total en equipos de escritorio es $ 4.324.194 y en notebooks, $ 5.160.000 .

### Puesta en producción

Para la puesta en producción necesitamos de un servidor donde alojar la aplicación: base de datos, archivos estáticos, almacenamiento de archivos, etc. Un nombre de dominio para acceder a ese servidor desde la web con los permisos básicos para la navegación segura. Herramientas de monitoreo de la aplicación, uso, visitas, performance y rendimiento del equipo, estabilidad y control de datos. (En general esto lo provee el mismo prestador del servicio de hosting)

**Aplicación dinámica de código personalizado**

PaaS: Railway. Se paga un mínimo de 5 U$ y luego se paga por el uso. Es decir, la cantidad de usuarios que entran a la aplicación. Consultas a la base de datos. Despliegue de varios ambientes, etc. Complicado estimar el costo mensual por fuera de esto sin tener un relevamiento supervisado de una aplicación en uso y escalar luego eso a una aplicación más grande.

## FASES DE PRODUCTO

Lo primero es Identificar un problema a resolver donde el producto este orientado siempre bajo una necesidad concreta de las actividades laborales-sociales cotidianas que sean repetitivas y tediosas, con una gran cantidad de horas que podrían evitarse y que sea factible de **automatizar** bajo un software.

Luego de eso hay que empezar a definir como lo vamos a prototipar, construir, testear y lanzar al publico de manera masiva, eso obviamente tiene distintas etapas de desarrollo continuo y no secuencial lineal.

## Prototipado

Excalidraw o Eraser AI

## Framework

Metaframework (enrutador, server side rendering, creacion de apis de back)

REACT: nextjs, (esta la OK)

VUE: nestjs

SVELT: sveltkit

ANGULAR: analogjs

## Back as a Service(BaaS)

SUPABASE y FIREBASE

-Autenticacion

-Conexion a base de datos

-Almacenamiento

## Componentes UI

Framework de CSS

Tailwind UI: [https://www.youtube.com/watch?v=2NuGuBpP5fo](https://www.youtube.com/watch?v=2NuGuBpP5fo)

Editores de código como cursor o windsurf

## Platform as a Service (PaaS)

Railway - Cloudflare

Vercel - Render - AWS Amplify - Heroku

## Pasarelas de pago

Paypal o Binance

Lemom squeezy

Paddle

## Monitoreo

PostHog - Mixpanel

## Aplicacion Mobile

React native - Expo extiende a React Native (OK)

Flutter

Ionic

Lynx

## Autenticacion de usuarios

Clerk

### FASE 1: Relevamiento y prototipado

Ver que necesito para hacer eso, servicios de pago, gratuitos etc.
### FASE 2: MVP

Mínimo producto viable (en menos de 3 meses)
### FASE 3: Beta

Detección de problemas, sacar lo que no se use (3 meses)
### FASE 4: Lanzamiento

Testeo principalmente de escalabilidad