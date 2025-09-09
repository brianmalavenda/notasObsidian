tipo:: [[Seguridad Red]]
tags:: #seguridad/red #dns 
fecha:: 2025-09-06
links:: [[01_Web/02_Fuentes de conocimiento/Blog desarrolladores/Cloudflare]] [[Alibaba DNS]] 
links_externos::

Nuestro proveedor de servicio de internet tiene un servidor de nombres configurado en el router. El servidor de nombres es una agenda telefónica en donde hace la relación entre el nombre de la página a la que queremos visitar y su número de identificación en la red (IP del servidor donde esta alojada la página, un número de esta forma 192.168.1.1). Es decir, el proveedor de internet tiene todas las paginas que visitamos en su servidor de nombres. Esto se usa para relacionar gustos, temas de interés y demás información que se puede obtener a través del comportamiento de navegación por la web. Solo sabiendo que páginas y con que frecuencia las visita.

Para evitar eso, vamos a configurar otro. Como mencionamos en el documento de estructura del internet, nuestra conexión de internet esta estrechamente vinculada a servidores de EE.UU, luego a Europa y a través de ellos al continente asiático en general. Esto tiene consecuencias en la navegación tanto en privacidad, seguridad y restricción de contenido en la red de internet global.

Cada vez que vamos a entrar a una página, tenemos que ir primero a un servidor de nombres. Depende cual acceda, la latencia (tiempo entre que envío un paquete de información y lo recibo) varía. Por ejemplo, los servidores DNS más comunes son Cloudflare, Google, después tenemos otros servicios públicos como Quad9 en Suiza. Esos son los primeros 3. Cuando vemos los de china los tiempos de latencia cambian muchísimo, pero el de Alibaba DNS no es tan malo. Por lo tanto vamos a poner ese a prueba.

![[Pasted image 20250828161006.png]]

![[Pasted image 20250828161022.png]]
![[Pasted image 20250828161028.png]]
Para eso vamos a ir al buscador en la barra de tareas y escribir “Estado de Red”. Una vez allí, en la ventana que nos abre vamos a ir a Cambiar opciones de adaptador.

![[Pasted image 20250828161040.png]]

Va a abrir una venta y aparecerá un ícono que haga referencia a una señal de Wifi si están coenctados por Wifi o (como en mi caso) un ícono con un cable Ethernet. Hacemos click derecho sobre el ícono y seleccionamos “Propiedades”. Abre una ventana en donde buscaremos entre las opciones tildadas “Protocolo de Internet versión 4 (TCP/IPv4)”. Seleccionamos esa opcion (hacemos clcik una vez) y se habilita abajo el botón “Propiedades”. Tocamos ahí, abre una nueva ventana donde tildaremos la opción “Usar las siguientes direcciones de servidor DNS”, lo que nos permitirá poner la IP del servidor DNS que querremos. Vamos a poner com esta en la foto que son los servidores de Alibaba.

![[Pasted image 20250828161048.png]]

Despues de hacer esto “Aceptar” y luego buscamos otra opción parecida a la anterior que se llama “Protocolo de Internet versión 6 (TCP/IPv6)”. Y hacemos lo mismo pero en este caso en Servidor DNS preferido va -> **2400:3200::1** y en Servidor DNS alternativo va -> **2400:3200:baba::1.** Una vez que terminamos ponemos Aceptar, despues Aceptamos los cambios en general en ambas conexiones y listo, podemos reiniciar la PC o hacer click derecho en el ícono de conexión y seleccionar “Desactivar”, esperar dos segundos para que se desactive la conexión y volver a hacer clcik derecho para Activarla de nuevo. Con esto reinicia la configuracion DNS

