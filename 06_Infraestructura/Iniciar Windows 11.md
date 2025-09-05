tipo::
tags::
fecha:: 2025-09-04
links::
links_externos::

Con la actualización de Windows 10 a 11, aumentó la telemetría de Microsfot en las computadoras. Por ese motivo, WIndows 11 desde el momento de la configuración inicial del sistema operativo te exige que vincules el dispositivo a un usuario para que toda acción con la computadora quede registrada en tu perfil.

![[Pasted image 20250904190143.png]]

Si queremos evitar esto tenemos que apretar SHIFT + F10 y se abrirá una consola de comandos. Allí colocaremos el siguiente comando para desconectarla de internet.

```
ncpa.cpl
```

Con esto abrirá la configuración de red de windows y nos permitirá desconectar a la computadora de la red. Luego con el siguiente comando reiniciaremos la computadora y esta vez nos permitirá
continuar la instalación sin conexión de red, es decir, que no nos exigirá logearnos.

```
oobe\bypassnro
```

Luego de esto se reiniciará la PC y terminaremos de configurar el sistema operativo sin problemas.




---
#### Referencias

