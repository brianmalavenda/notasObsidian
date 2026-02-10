tipo:: #web
tags:: #hosting #hosting_tipos #blog_desarrolladores
links:: [[Conexión con hosting a través de SSH]] [[Delegar dominio]] [[01_Web_2.0/Registrar dominio]] 
links_externos::
# INDICE

![[#^8519e2]]
![[#^24a3e4]]
![[#^43ca30]]
![[#^cc0ba8]]
![[#^2d4a7e]]
![[#^ebab66]]
![[#^f6c574]]
![[#^7aa654]]
![[#^9d5c26]]

## ¿Que es un Hosting?

^8519e2

**Hosting** es un servicio que te permite **alquilar un espacio en un servidor conectado a internet** para guardar los archivos y la base de datos de tu sitio web.

Ese servidor funciona como la “casa” de tu página: allí viven el código, las imágenes, los estilos y todo lo que hace que tu web se muestre cuando alguien escribe tu dominio en el navegador.

En términos prácticos:
- El **dominio** (ejemplo.com) es la dirección de tu casa.
- El **hosting** es el lugar físico (servidor) donde está construida la casa.
- El **sitio web** son los muebles, paredes y decoración que la gente ve al entrar.

## Hosting compartido

^24a3e4

Un servicio de hosting compartido aloja múltiples sitios web en un único servidor físico, de modo que todos comparten los mismos recursos (CPU, RAM, ancho de banda y sistema de ficheros)

**Ventaja**: coste muy bajo y administración simplificada.

**Desventaja**: rendimiento variable si otros “vecinos” consumen muchos recursos.

**Provedores**: Hostinger, SiteGround

## Hosting dedicado

^43ca30

En el hosting dedicado el cliente arrienda uno o varios servidores físicos exclusivos, sin compartirlos con nadie más

**Ventaja**: control total de hardware, sistema operativo y seguridad.

**Desventaja**: precio elevado y responsabilidad completa de administración.

**Provedores**: Bluehost, Liquid Web

## VPS (Servidor Privado Virtual)

^cc0ba8

Un VPS es una máquina virtual con recursos (CPU, RAM, disco) garantizados, creada mediante un hipervisor sobre un servidor físico que aloja varias de estas instancias aisladas. Ejemplo: Amazon Web Services

**Ventaja**: balance entre coste y control; root access.

**Desventaja**: requiere conocimientos de administración de servidor.

**Proveedores:** Hostinger VPS, KnownHost

## PaaS (Platform as a Service)

^2d4a7e

PaaS ofrece un entorno gestionado por un proveedor en el que sólo te ocupas del código: hardware, sistema operativo y runtime están abstraídos

**Ventaja**: despliegue rápido, escalado automático y sin tareas de infra.

**Desventaja**: menor control de bajo nivel y posible “vendor lock‑in”.

**Azure:** AWS Elastic Beanstalk, Azure App Service, Jeroku

## Sitios estáticos

^ebab66

El hosting de sitios estáticos sirve únicamente archivos HTML, CSS y JavaScript sin ejecutar código en servidor; cualquier lógica corre en el cliente o en APIs externas. Reddit

**Ventaja**: alta performance, seguridad y coste mínimo.

**Desventaja**: no apto para funcionalidades dinámicas sin apoyo de servicios externos.

**Proveedores:** Cloudflare Pages, Netlify

## Object Storage

^f6c574

El almacenamiento de objetos gestiona datos no estructurados (imágenes, vídeos, logs) como “objetos” con metadata y un identificador único, distribuidos en un repositorio virtual. Ejemplo: Digital ocean, AWS(S3)

**Ventaja**: escalabilidad masiva y alta durabilidad.

**Desventaja**: latencias más altas que un sistema de archivos tradicional.

**Proveedores:** Amazon S3, Google Cloud Storage

## Backend Serverless

^7aa654

El backend serverless (BaaS/FaaS) permite ejecutar funciones o servicios (bases de datos, autenticación) sin aprovisionar servidores, facturándose sólo por uso.

**Ventaja**: sin administración de infra, escalado instantáneo y pago por invocación.

**Desventaja**: límites de tiempo de ejecución y posible complejidad para arquitecturas muy grandes.

**Proveedores:** AWS Lambda, Google Cloud Functions

## Email Server

^9d5c26

Un servidor de correo es el sistema que envía, recibe y almacena emails, actuando como intermediario entre clientes de correo y otros servidores SMTP/IMAP/POP3

**Ventaja**: control completo de políticas de seguridad y deliverability.

**Desventaja**: requiere configuración cuidadosa para evitar spam y gestionar certificados.

**Proveedores:** Google Workspace, Microsoft 365

La comunicación directa entre nodos clientes y servidores, exponía en una capa sin seguridad a los servidores de ataques DDoS(millones de peticiones al servidor para colapsarlo). De esta manera entre otros tipos de ataques maliciosos llegan directo al servidor, hay que invertir en seguridad para filtrar esos millones de peticiones, filtrar dominio, etc. Lo que hace Cloudflare es generar un nodo intermedio de la información de tu servidor en donde esten los nodos clientes para que las solicitudes tengan un intermediario (data center de Cloudflre) donde ellos se encargan de la seguridad de la red para estos ataques DDoS o de bots maliciosos. Centros de datos en más de 335ciudades de todo el mundo.

![](file:///C:/Users/brian/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)

[^1]: 
