tipo:: #tecnico/reparacion 
tags::
fecha:: 2025-12-23
links:: [[HP]]
links_externos::

---

nombre::julieta
dispositivo:: [[HP]] laptop 15-gw0xxx laptop-jinqsek8
fecha:: 2026-07-30
sistema:: [[Windows_11]]
procesador:: AMD Athlon Silver 3050U with Radeon Graphics (2.30GHz)
ram:: 4 GB, 3,44 utilizable
rom:: 240 SSD -> 240 SSD
diagnostico:: error al cargar el sistema operativo
que_hacer:: formatear

La computadora no cargaba el sistema operativo. Se formateo creyendo que había sido un error en la instalación de alguna actualización del sistema operativo. Dos veces luego de la instalación se colgó abruptamente con un mensaje de error de disco.  
Cambiamos el disco PATRIOT BURST reciclado de una notebook vieja por otro de 240GB KINGSTON también reutilizado de una notebook vieja.

---
fecha:: 2025-12-12
diagnostico:: el sistema operativo tarda mucho en iniciar
que_hacer:: formatear
rom:: 1 TB -> 240 SSD

la CPU y la ram se mantienen estables despues del inicio de sesion, pero el DISCO HDD tiene mesetas de un periodo considerable que esta al 100% con valles del 20% o un poco menos. La exigencia de escritura/lectura para el disco mecánico no lo soporta y tiene este overloop

Solución: 
Se cambio el disco HDD por uno de una laptop usada que tiene de capacidad 250 GB. Con esto el disco que estaba a 100% todo el tiempo pasó a estar cómodo a 10%. Queda agregar 4gb de RAM y con eso estará OK.

Se instaló Windows 11 la última distribución desde la página oficial. Se instaló la versión 2021 del Office y se activó.

Configuración base
1. [x] Configuración de sistema operativo sin vinculación con cuenta de Microsoft  
2. [x] Desinstalar aplicaciones de Windows 11
	1. [x] Asistencia para juegos
	2. [x] Centro de opiniones
	3. [x] Copilot
	4. [x] El tiempo
	5. [x] Family
	6. [x] Microsoft Clipchamp
	7. [ ] Microsoft Bing - > finalizar posibilidad de que se ejecute en segundo plano
	8. [x] Journal
	9. [x] Mensajes del operador
	10. [x] Microsoft News
	11. [x] Microsoft Team
	12. [x] Microsoft To Do
	13. [x] Microsoft Whiteboard
	14. [x] Power Automate
	15. [x] PC Manager
	16. [x] Planes móviles
	17. [x] Xbox
	18. [x] Xbox Live
3. [ ] Desactivación de telemetría de Windows
		Los scripts que teníamos para Windows 10 y primeras versiones de Windows 11 no funcionaron para ofuscar la observabilidad del sistema operativo. En esta última actualización de Windows 11 han bloqueó la posibilidad de crear reglas de firewall que bloqueen a los servidores de microsoft.
4. [x] Desactivar Cortana y OneDrive de Microsoft para evitar sincronización de archivos
5. [x] Instalación de navegadores Brave y Wolf
	Documentos > Notas > 00_Navegadores orientados a privacidad
6. [x] Aplicación de DNS de Alibaba y dejamos nota para cambiar la configuración en 
	Documentos > Notas > 01_Configuración DNS
7. [x] Instalación de LibreOffice 
	Documentos > Notas > Instalables
8. [ ] Instalación de Proton VPN y documentación para utilizar
		No pudimos crear una cuenta de proton porque necesitamos validar con otro mail. Tenemos que dejar creado un tutorial para que lo pueda hacer el usuario directamente. 
		Dejamos un documento con la explicación de como crear una cuenta en Proton.
	Documentos > Notas > 02_Proton VPN
	Documentos > Notas > 03_Crear cuenta proton


---
#### Referencias

