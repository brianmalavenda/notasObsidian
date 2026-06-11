tipo:: [[Seguridad Ordenador]] [[APT36]] 
tags:: #seguridad/ordenador/archivos_impostores #web/seguridad 
fecha:: 2025-08-29
links::
links_externos:: [bleepingcomputer](https://www.bleepingcomputer.com/news/security/apt36-hackers-abuse-linux-desktop-files-to-install-malware/) 

Los usuarios abren el archivo .desktop pensando que es un PDF, lo que hace que un comando bash oculto en el campo '**Exec=' cree un nombre de archivo temporal en '/tmp/**' donde escribe una carga útil codificada en hexadecimal obtenida del servidor del atacante o de Google Drive.

Luego, ejecuta 'chmod +x' para hacerlo ejecutable y lo inicia en segundo plano.

Para reducir las sospechas de la víctima, el script también inicia Firefox para mostrar un archivo PDF señuelo benigno alojado en Google Drive.

la manipulación del campo 'Exec=' para ejecutar una secuencia de comandos de shell, los atacantes también agregaron campos como 'Terminal=false' para ocultar la ventana de terminal al usuario y 'X-GNOME-Autostart-enabled=true' para ejecutar el archivo en cada inicio de sesión.

![Un archivo de escritorio malicioso](https://www.bleepstatic.com/images/news/u/1220909/2025/August/desktop.jpg)

En los ataques APT36, los atacantes abusan de este mecanismo de lanzamiento para convertirlo esencialmente en un sistema de instalación de malware y de persistencia, de forma similar a cómo se abusa de los accesos directos 'LNK' en Windows.

![[Pasted image 20250829133412.png]]

---
#### Referencias

