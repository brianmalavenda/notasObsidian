tipo:: #servidor 
tags::
fecha:: 2025-09-17
links::
links_externos::


La idea sería poder hacer de una raspberry pi 5 un servidor. Esta placa tendría un disipador y un cooler para que no levante temperatura. La unidad tambien debería contar con su propia carcasa para evitar que se ensucie o se dañe al estar expuesta. 

## Arquitectura 

Grafico de servidor: https://www.youtube.com/watch?v=kzAVaNgXuIk
https://www.youtube.com/watch?v=hM-CRljDSUY

Necesitamos un procesador con arquitectura x86 ARN?, ver esto. Pero la Raspberry pi 5 no tiene esta arquitectura en su micro. La parte de ser servidor de peliculas y hacer streaming de ellas no se va a poder. Para servir webs ligeras, no va a haber inconvenientes.

[ Raspberry Pi 5 ]
│
├── Sistema Operativo: Raspberry Pi OS 64-bit (LITE si puedes, sin GUI)
├── Almacenamiento del SO: SSD NVMe o SATA (vía USB 3.0) — mínimo 32GB
├── RAM: 8GB — monitorea uso con htop/glances
│
├── Docker + Portainer (gestión visual de contenedores)
│   ├── Contenedor 1: Tu app web colaborativa (ej: Node.js + PostgreSQL + Redis)
│   ├── Contenedor 2: Nginx (reverse proxy + SSL con Let’s Encrypt)
│   ├── Contenedor 3: Jellyfin (con montaje del DAS para películas)
│   └── Contenedor 4: Watchtower (actualización automática de contenedores)
│
├── DAS Terramaster D4-320
│   └── Montado en /mnt/media (películas) y /mnt/backups
│
├── Red: IP fija + redirección de puertos en router + DDNS (no-ip, duckdns)
├── Seguridad: Fail2ban, firewall (ufw), actualizaciones automáticas
└── Energía: UPS (obligatorio) + apagado automático ante fallo de energía
###### **Escalabilidad Futura**

1. **Migra la app web a un VPS barato** (Hetzner, Contabo, Oracle Free Tier) y deja en la Pi solo Jellyfin + almacenamiento.
2. **Añade un segundo nodo** (otra Pi o miniPC x86) para balanceo o servicios específicos.
3. **Usa Cloudflare Tunnel** para exponer tu servidor sin abrir puertos en el router (más seguro).

## Software 

1. TrueNAS: https://www.youtube.com/watch?v=zWHiz71xdfo
2. OpenMediaVault: https://www.youtube.com/watch?v=vySCnJ8TyCw
3. CasaOS: https://www.youtube.com/watch?v=27IiYo37lww
4. Nextcloud: https://www.youtube.com/watch?v=mQhbkOKpPTU

## Equipamiento

https://www.youtube.com/watch?v=SqJ7VQ-4E4U

https://tiendamia.com/ar/producto?amz=B0CRSNCJ6Y&pName=Raspberry%20Pi%205%20Starter%20Kit%20PRO%20-%20Turbine%20Black%20&lpar;128GB%20Edition&rpar;%20&lpar;8GB%20RAM&rpar;

CanaKit Raspberry Pi 5 Kit de Inicio PRO - Negro Turbina (Edición de 128GB) (8GB RAM)

**$ 399.000** (con costo por kilo incluido)


https://www.mercadolibre.com.ar/raspberry-pi-5-8gb-ram-potente-mini-pc-para-proyectos-iot-servidores-y-desarrollo-con-usb-30-pcie-y-soc-de-alto-rendimiento/p/MLA32625223#polycard_client=search-nordic&search_layout=grid&position=3&type=product&tracking_id=954bd228-a296-4dc8-9e39-e4bf2bf6b2a4&wid=MLA1760024408&sid=search

 Raspberry Pi 5 8GB RAM - Potente Mini PC para Proyectos, IoT, Servidores y Desarrollo con USB 3.0, PCIe y SoC de Alto Rendimiento

**$145.000**
#### Hat SSD

##### Mini
https://www.mercadolibre.com.ar/hat-pcie-a-m2-nvme-ssd-high-speed-para-raspberry-pi-5/up/MLAU3311685982#polycard_client=search-nordic&search_layout=stack&position=7&type=product&tracking_id=10a75e28-e9ad-4181-910b-83222bf4def7&wid=MLA2188826062&sid=search

**$15.000**

##### Larga
https://articulo.mercadolibre.com.ar/MLA-1889213514-pcie-a-m2-ssd-shield-para-raspberry-pi-5-_JM#polycard_client=search-nordic&search_layout=stack&position=11&type=item&tracking_id=f49a2987-8822-4bdb-b807-a85fe182d829&wid=MLA1889213514&sid=search

**$ 54.000**

#### SSD M2 NVME

##### Mini
https://www.mercadolibre.com.ar/ssd-disco-2242-pcie-30-x-4-m-key-wicgtyp-256-gb-3d-nand/up/MLAU382796738#polycard_client=search-nordic&search_layout=stack&position=4&type=product&tracking_id=37d4491b-820e-4d31-bd98-f34ce9525a07&wid=MLA1797172006&sid=search

**$ 65.000**

##### Larga
https://www.mercadolibre.com.ar/ssd-m2-250gb-western-digital-nvme-2280-pcie-gen3-2400mbs-color-verde/p/MLA26032006#polycard_client=search-nordic&search_layout=stack&position=27&type=product&tracking_id=7cf86365-742a-49e8-80d8-fc931e406840&wid=MLA2041803146&sid=search

**$ 43.000**


#### Carcasa
https://www.mercadolibre.com.ar/gabinete-carcasa-case-riel-din-raspberry-5-metalico-ws/p/MLA50965640#polycard_client=search-nordic&search_layout=grid&position=8&type=product&tracking_id=c33d029e-6a0d-40d2-a0d0-c7a297aad3d4&wid=MLA2104036464&sid=search

**$ 30.000**

### Cooler
https://www.mercadolibre.com.ar/raspberry-pi-5-ventilador-enfriador-disipador-cooler-activo/up/MLAU3000498558#polycard_client=search-nordic&search_layout=stack&position=6&type=product&tracking_id=e60b7382-62ed-403b-ae69-2f49f2645059&wid=MLA2007914990&sid=search

**$ 13.000**


## Almacenamiento

### Version 1

https://tiendamia.com/ar/producto?amz=B0CTTL9R7Z&pName=TERRAMASTER%20D4-320%20External%20Hard%20Drive%20Enclosure%20-%204bay%20USB%203&period;2%20Gen2%2010Gbps%20Type-C%20USB%20Storage%20Hot%20Swappable%20Plug%20and%20Play%20&lpar;Diskless&rpar;

**$ 384.000**

Si tenemos un DAS de este tipo tengamos  en cuenta la diferencia de transmision por los periféricos de entrada/salida del NAS y la computadora. La Pi 5 tiene USB 3.0 (5Gbps), así que **no aprovecharás el 10Gbps**, pero igual es suficiente para streaming y almacenamiento. Los **discos NAS** (WD Red, Seagate IronWolf) dan mayor durabilidad.

https://www.mercadolibre.com.ar/disco-solido-externo-de-ultra-alta-velocidad-4tb-ssds/up/MLAU3422693187#polycard_client=search-nordic&search_layout=stack&position=18&type=product&tracking_id=174c9663-9c29-4e20-b072-c99e496ae14e&wid=MLA1535685987&sid=search

Disco Solido Externo De Ultra Alta Velocidad 4tb Ssds Rojo

**$ 38.000**


### Version 2

NAS Sinology + 4 Seagate Iron Wolf de 4 TB c/u en RAID 10

US$350 [NAS Sinology](https://www.ebay.com/itm/317484626458?_skw=Synology+DS423&itmmeta=01K8XE71BZ11XDWY69SHGX4B2B&hash=item49eb8ef21a:g:pesAAeSwyixpBAxE&itmprp=enc%3AAQAKAAAA8FkggFvd1GGDu0w3yXCmi1c7onQjcplmGSXFkF45bALUmHgPnpjm3KW83YU14qlgNdOdJgWNRZC9uz1qLAJE7K0BFOosq7WxnbK6T5%2F2%2Feqp8AK4YTz90at5vcnUoTo5WXfUdZPe1Yq9sXjlG1BAtaBID4GeMk%2FDjoKuzhQPZUOpwCBZNPU35HhTnth1gzzp1eizts1Q02y4f%2F5GXoxSAD91ISV878igfAPPrltJh%2Ba8Oan%2BiCaHsiPkfLIuPXnh8LMpwrPXsYifymOcoKjj8h1YzoOHaCJqb1FLvpn2Z2mk5up2si5ut3V5HiH78yGcMw%3D%3D%7Ctkp%3ABFBMkJacrsdm)
US$500 - US$200 * 2 [Seagate Iron Wolf de 4 TB](https://www.ebay.com/itm/226710130442?_skw=Seagate+IronWolf+Pro+4tb&itmmeta=01K8XEHGY1GPKV2HXXHR2YC4JV&hash=item34c8fa0f0a:g:ugsAAOSwCSloAnlZ&itmprp=enc%3AAQAKAAAA0FkggFvd1GGDu0w3yXCmi1fLCx2NHW%2BlfKSYSWElJNup16oAeqCXau2SVGKnGcFUlxFCodpEX8kBXi8aJN2Zmwg4B%2FidB4zCJnNqKK8ohNLrCsLxQy5KqMEOSn6q3r4dNHVunT94MmKYHhItM5bq4ay16p47pzocpPi7wq2LIHTiPq4uz0568M89cdJWAB1TTa8yMBlsNKYa%2FDd%2FsKh45EQBx3HE1hPZDq82XyJAqWd8un4Ex2cLvwLEMTeIyGaqoKkmSX4%2FKe%2F491a7SvymiFY%3D%7Ctkp%3ABk9SR5iPxq7HZg)



## Seguridad

https://www.youtube.com/watch?v=_Hcm9ksOkqU

## MINI PC

##### Presupuesto
https://es.ylipc.com/product/powerful-mini-pc-barebone#RootGrid2214_d0ef46250e2e4b55676b172e5b8926e63


#### Notebook Pro
https://notebookspro.ar/producto/mini-pc-beelink-mini-s-intel-celeron-n5095-ssd-256-gb-8-gb-ram/

**$ 320.000**

https://notebookspro.ar/producto/mini-pc-msi-cubi-n1-intel-n100-ssd-256-gb-8-gb-ram-4-nucleos/

**$ 320.000**


https://www.mercadolibre.com.ar/mini-pc-performance-ryzen-3-3200u-8g-ram-240gb-ssd-hdmi-wifi-bluetooth/p/MLA47213548#polycard_client=search-nordic&search_layout=stack&position=3&type=product&tracking_id=99e2b8eb-0822-46a8-a98e-630a3ca16422&wid=MLA2032854792&sid=search

$ 280.000

https://www.mercadolibre.com.ar/b-max-b6-plus-intel-core-i3-windows-11-intel-iris-plus-graphics-12-gb-512-gb-dark-green/p/MLA29557085?product_trigger_id=MLA44123036&picker=true&quantity=1

**$ 310.000**

## Consulta

Hola experto de infraestructura, quiero hacerte una consulta como mi mentor para iniciar un servidor web casero. Quiero que utilices toda tu experiencia acumulada de haber hecho servidores webs y me la proporciones para establecer una opinion que ayude a concretar mi proyecto de la manera más profesional siendo efectivo en la utilizacion de recursos.

Primero: Mi objetivo
Quiero crear un servidor web que tenga la posibilidad primero de servir páginas en internet que esta apuntado a un trafico de usuarios bajo pero que trabajan en linea de forma coordinada. Es una página que sera como una plataforma de trabajo para escritura cooperativa como ofrece google con su servicio de google docs.

Mi segundo paso es que una vez que tenga esa aplicacion web subida y se empiece a utilizar. Quisiera poder disponibilizar en una plataforma como plex o alguna otra de código abierto que me permita subir peliculas y que unos 10 usuarios puedan ver en paralelo. 

Idea para cumplir mi objetivo:
Empece con esta idea que es la que quiero charlar contigo. Los recursos para eso entiendo que puedan ser satisfechos con una raspberry pi 5, con un almacenamiento interno de 128 gb y una ram de 8 gb con un gabinete que tenga un cooler y disipador del procesador. Ademas que cuente con una conexion a la red de energía y a futuro a reemplazar por un UPS. Esta "mini PC", quiero evaluar si tiene que estar con un sistema operativo como TrueNAS o CasaOS o dejar el sistema operativo nativo de raspberry. Luego, la idea continuaría con tener la posibilidad de almacenar esas peliculas. Para eso, pensaba en un DAS con un TERRAMASTER D4-320 External Hard Drive Enclosure - 4bay USB 3.2 Gen2 10Gbps Type-C USB Storage Hot Swappable Plug and Play. Esto estaría conectado de manera permanente a mi raspberry.

Bueno, empecemos por aqui. Que opinas?



## Respuesta

#### **Punto 1: El "Corazón" del Servidor - Hardware**
    
- **Solución Propuesta:**
    
    - **Opción A (Ideal):** Considera una **Mini PC con un procesador Intel de generación reciente (con GPU Intel Quick Sync)**. Puedes encontrar mini PCs usadas (Dell OptiPlex, Lenovo ThinkCentre) o nuevas (de marcas como Beelink, Minisforum) por un precio similar a un kit RPi5 + accesorios. La ventaja brutal es que Intel Quick Sync es excelente para transcodificación. Plex y Jellyfin lo aprovechan al máximo, permitiendo fácilmente 10+ transcodificaciones simultáneas con un consumo de energía aún bajo.
    
	- **¿Qué es la transcodificación?** Ocurre cuando el dispositivo que reproduce el video (una TV, un celular) no es compatible con el formato original del archivo de la película (por ejemplo, un MKV con códec H.265). El servidor (Plex/Jellyfin) debe convertir el video "al vuelo" a un formato compatible. Este proceso consume muchísima CPU.
	    
	- **El Rendimiento Real:** Un Raspberry Pi 5, aunque muy mejorado, **no podrá transcodificar 10 streams de video simultáneamente**, y probablemente ni siquiera 2 o 3 si son de alta calidad (1080p o 4K). Simplemente no tiene la potencia de procesamiento de un CPU de escritorio (como un Intel Core i5 o superior).
	    
	- **La Solución (Direct Play):** Tu objetivo debe ser que el **100% de las reproducciones sean en "Direct Play"** (Reproducción Directa). Esto significa que el servidor solo envía el archivo tal cual, y el dispositivo cliente hace todo el trabajo. Para lograr esto, necesitas que tus dispositivos (TVs, celulares) sean compatibles con los formatos de tus películas. La mayoría de los dispositivos modernos lo son.
          

#### **Punto 2: El "Cerebro" - Sistema Operativo**

- **TrueNAS Scale:** Es un sistema operativo excelente, pero pesado. Está optimizado para x86_64 y consume recursos significativos (RAM y CPU).     
- **CasaOS:** Es más ligero y está diseñado para la RPi, pero es una capa de abstracción. Te aleja de aprender cómo funcionan realmente los servicios por detrás.
       
    - Instala el sistema operativo oficial de Raspberry Pi (**Raspberry Pi OS Lite** - la versión sin escritorio) o una distribución Linux minimalista como **Debian Server** o **Ubuntu Server**.        
    - **Gestiona los servicios con Docker.** Esta es la clave de la profesionalidad y eficiencia.
        - **¿Por qué Docker?** Cada servicio (la app web, la base de datos, Plex/Jellyfin) correrá en un contenedor aislado. Es fácil de instalar, actualizar, respaldar y no ensucia tu sistema operativo principal.
        - Usa `docker-compose` para definir y gestionar todos tus contenedores con un simple archivo YAML.

#### **Punto 3: La "Estrategia" - Implementación por Fases**

**Fase 1: La Infraestructura Base (2-3 semanas)**
1. Instala el SO minimal (ej: Raspberry Pi OS Lite) en el SSD externo.    
2. Configura IP fija, hostname, SSH, actualizaciones.
3. Instala Docker, Docker-Compose y Portainer.
4. Configura el DAS. Crea un volumen RAID (ej: RAID 5 o RAID 10) para redundancia. Formatea con un sistema de archivos robusto como ext4 o Btrfs. Formatear en ext4, monta en /mnt/media.
5. Monta el almacenamiento del DAS en tu sistema de forma automática al iniciar (editando el archivo `/etc/fstab`).
6. ==**Configura un Servicio de Reverse Proxy (ej: Nginx Proxy Manager).** Esto es FUNDAMENTAL para exponer servicios web de forma segura. Te permitirá usar nombres de dominio (ej: `docs.midominio.com`, `plex.midominio.com`) y manejar certificados SSL (HTTPS) fácilmente con Let's Encrypt.== 
    

**Fase 2: La Aplicación Colaborativa (3-4 semanas)**
1. ==Investiga la aplicación específica. ¿Será un desarrollo propio o usarás una solución open-source existente como **Etherpad**, **Nextcloud + Collabora Online**, o **OnlyOffice**?==
2. Implementa la aplicación usando Docker. Normalmente necesitarás al menos dos contenedores: uno para la app y otro para la base de datos (MySQL/PostgreSQL).
3. ==Configura el Reverse Proxy para que apunte a tu aplicación.==
4. **¡Haz backups!** Configura backups automáticos de los volúmenes de Docker que contienen los datos de la app y la BD.

**Fase 3: La Plataforma de Streaming (1-2 semanas)**
1. Una vez estable la Fase 2, instala **Jellyfin** (recomendado, 100% open-source) o Plex en un contenedor Docker.    
2. ==**Evita la transcodificación**: Obliga a que los clientes reproduzcan en formato original (requiere que los dispositivos soporten el códec del archivo). Esto reduce la carga al 10%. 1. **Pre-transcodifica tus archivos**: Convierte toda tu biblioteca a H.264 + AAC en contenedor MP4 con resolución adecuada (720p o 1080p). Así, el servidor solo hace “streaming directo” sin procesar.==
3. Apunta la biblioteca de Jellyfin/Plex a la carpeta de películas en tu DAS.
4. Configura el Reverse Proxy para el subdominio de streaming (ej: `pelis.midominio.com`).
5. Monitorea recursos (htop, glances, netdata).


#### Alcance de uso

 **Límite de usuarios simultáneos**: 10 es demasiado. Con una Pi 5, 2-3 usuarios simultáneos con streaming directo es lo máximo realista. Si insistes en 10, necesitarás un x86_64 con GPU dedicada (Intel Quick Sync o NVIDIA).

### Estado


Nmap scan report for localhost (127.0.0.1)
Host is up (0.000056s latency).
Not shown: 992 closed tcp ports (reset)

PORT     STATE SERVICE
111/tcp  open  rpcbind
8000/tcp open  http-alt sitio de los famosos 50
8080/tcp open  http-proxy admin de OMV
8088/tcp open  radan-http  NGINX no se pudo conectar (debe ser la del ejes)
8090/tcp open  opsmessaging  base de datos de WP
8181/tcp open  intermapper NGINX
9000/tcp open  cslistener portainer
9001/tcp open  tor-orport

---
#### Referencias

