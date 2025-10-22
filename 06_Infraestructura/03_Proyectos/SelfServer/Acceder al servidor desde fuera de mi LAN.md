tipo::
tags::
fecha:: 2025-10-17
links::
links_externos::


1- Una de las formas es abriendo puertos directamente desde el router. Por lo que vi es poco recomendado hacer esto por cuestiones de seguridad.
2- La siguiente es crear un tunel de acceso sin abrir puertos del router. Esto parece en un principio lo más conveniente al inicio.
3- La mas segura pero compleja es crear una VPN de acceso, que ahi tenemos que ver como se hace.

>[!NOTA]
Se me ha cruzado crear una [[DMZ (Zona Desmilitarizada)]] para tener separada una red que ofrezca servicios para acceder por fuera, pero comprendí que como tengo un servidor (todo en uno) hoy en día esto sería una complejidad innecesaria para mi infraestructura actual.


Vamos a avanzar ahora con la **opción 2** y crear un tunel de acceso. 

## Entunelamiento o tunelling


certificados
Crear llaves de acceso ssh para la conexion con el servidor. Esto por convención usa los puertos 21/22. 

Nos basamos en este video para ver algunos conceptos de seguridad de redes para el servidor https://www.youtube.com/watch?v=rhZF1_O5l84.
Acá tiene definiciones de una estacion de trabajo para pentesting con kali linux con la configuracion de ip's siguiente
-lookback
-firewall - vyos [¿Que es?]
controlador de dominio [¿Que es?]

```bash
sudo nmap -sn [rango de ips] -n
# descripcion de opciones en el comando nmap
# sn -> isnp : un echo request para obtener un echo replay
# n -> no aplique resolucion de DNS
# -sS -> estado
# -sV -> servicio
```

explotar un puerto ssh

```

```

1-que es LAN/DMZ? lo vi en en opciones disponible de configuración de mi router y me quedé con la duda de saber que es
2-definición correcta de WAN
3-DNS dinámico


## Túnel HTTP
### ngrok
Para eso una de las herramientas que encontramos es **ngrok** que te crear un tunel y te disponibiliza un dominio con certificado ssl para acceder por el protocolo https a tu aplicación alojada en tu servidor desde la web.

### Cloudflare Tunnels

Cloudflare Tunnels es otra forma con un dominio publico. 
https://www.youtube.com/watch?v=wO_MkRetgnE
ZeroTrus -> Networks


## Túnel SSH reverso

Esta opción es mas un canal abierto entre una computadora cliente con el servidor donde para ambos se configura usando ssh un tunel de mensajes de ida y vuelta entre ellos. Para exponer una app desde mi servidor y que quede publica para acceder por cualquier computadora cliente desde un navegador esto no me sirve. Hay que ver que aplicaciones puede tener, quizas para control, alertas, etc donde mi computadora cliente desde donde monitoreo mi servidor pueda recibir mensajes de el aún no estando con la sesion iniciada, por ejemplo en openmediavault. Esto tiene mas pinta de funcionar para estos casos de uso.

---
#### Referencias

