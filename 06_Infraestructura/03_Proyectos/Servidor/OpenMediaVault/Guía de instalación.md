tipo:: #servidor/config
tags::
fecha:: 2025-10-13
links::
links_externos::


1. La notebook que estoy usando para la primer prueba no tiene placa de red. Omití la instalación del driver y luego conecte mi adaptador de USB-RJ45 para conectarme luego a la red.
2. En el momento de la instalacion, la configuración de red de OMV, no pudo encontrar el servidor DHCP porque no esta conectado a ninguna red, por lo tanto no sabe con que IP se va a comunicar con los demás dispositivos, como en el anterior paso no le configuré nada esto lo vamos a tener que hacer manualmente despuesto. 
3. Pide que ingrese manualmente una IPv4 para la computadora que va a oficiar de servidor. Para esto tengo que ver mi red DHCP el rangos de IP que esta configurado en mi router y luego de eso definir una IP fija para la compu.
	1. Entrar a mi router
	2. Revisar rango de ips en mi servicio DHCP (Conseciones de DHCP)
	
	```
	Nombre de host	Dirección MAC	Dirección IP	Caduca en
	brian	1c:87:2c:43:38:e1	192.168.0.7	00h48m44s
	OPPO-A40	26:65:f4:08:60:4a	192.168.0.4	00h40m40s
	```

	1. Asignar una IP a mi servidor de laboratorio. En este caso le asigno la 192.168.0.10
	2. Me pide luego la máscara de red. Para eso entro en información general de la configuración de red y miro cual tengo, tengo 255.255.255.0, le pongo esa.
		VER ESTA DEFINICIÓN QUE ME TIRÓ DEEPSEEK
		==La Máscara de Red (255.255.255.0): Define los límites de tu "calle privada". Le dice a tu servidor: "Todas las direcciones que empiecen por 192.168.1.XXX están en mi red local, para el resto, usa el Gateway".
		==
	3. Luego me pide un "encaminador de pasarela" que es en este caso mi router. Podría ser un servidor proxy en adelante. Es decir, tengo que poner 192.168.0.1
	4. Luego me pide un servidor de nombres, voy a usar el de mi router que utiliza uno ya configurado por la compañia provedora de internet (Telecentro) en mi caso.
	5. Nombre del servidor srv-sanmartin901 (900 para arriba van a ser pruebas)
	6. Nombre del dominio, le pongo "local"
	7. Clave del superusuario: 
	8. Aca tengo un problema porque seleccione idio español-españ y cuando me quizo definir zona horaria me tiraba de españa. Esto capaz me provoque problemas despues.
	9. Toca elegir una distribución cercana al país, vamos a elegir en Argentina la réplica deb.debian.org. Me da a elegir que ponga un proxy HTTP para conectarme. Dejo en blanco. 
		==FALLÓ LA CONFIGURACION DE LA REPLICA. CAPAZ QUE ES POR LA CONEXION A INTERNET==
		Seguro es eso, había una opcion de introducir manualmente. Vamos a tener que ir por esa.
		deb.debian.org -> /debian/ en blanco el proxy
		http://[[user][:pass]@]host[:port]/
		==FALLO DENUEVO==
	10. Pense que llegue hasta acá porque la instalación require conexion a internet para descargar la réplica de Debian si o si. Pero me ofreció una opción de continuar sin replica en red. Me advirtió "si está instalando utilizando una imagen de CD de arranque de red "netinst" y no elige una réplica acabará con una instalación de un sistema base muy reducida. ¿Desea continuar sin ua réplica en red?" Decimos que sí y continuamos.
	11. Dijo que se instaló todo ok y que había que remover la unidad de disco, asi que hicimos eso. Vamos a ver como sigue.

Ya se instaló correctamente y después de logearme en la terminal de mi servidor, por una pc cliente puedo iniciar sesión y conectarme al dashboard y demás recursos de mi server.

Allí instalé todos los widgets y luego cambie la contraseña del usuario. Al hacer esto y dejarla inactiva por un tiempo, cuando "volví" tenía un error 503 en la pagina de mi servidor y no pude volver a entrar. Pienso que tiene que ver con la actualización de IPs de mi red local de DHCP. Tuve que cambiar la configuración de la IP de mi máquina cliente a "automático" para que el servidor DHCP (mi router) me asigne una IP válida.

Instalar los extras después de armar el sistema de archivos con los discos que tenemos conectado a la máquina.
Para instalar los extras tenemos que ir aca: https://wiki.omv-extras.org/ y conectarnos por SSH al servidor

para eso abrimos un powersheel en windows o una terminal de linux y ponemos 
```
ssh root@[nombre del dominio.local]
```

una vez que nos conecte escribimos este comando en la terminal

```
wget -O - [https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install](https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install "https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install") | bash
```


---
#### Referencias

