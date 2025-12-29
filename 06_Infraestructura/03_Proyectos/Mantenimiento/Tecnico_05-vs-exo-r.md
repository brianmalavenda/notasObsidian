tipo:: #tecnico/reparacion 
tags:: 
fecha:: 2025-12-23
links:: [[Exo]]
links_externos::

---

nombre:: vs
dispositivo:: notebook exo
sistema:: windows 10
ram:: 4gb
rom:: 250 gb
- [ ] nueva
diagnostico:: La notebook no enciende, el cargado cuando esta enchufado provoca la luz la placa indicando que llega electricidad hasta alli.
que_hacer::  Desconectamos la batería, cambiamos la pila de la placa CR2032 por una nueva. 

![[notebook vs-exo.png]]


**Mantené presionado el botón de encendido por 30 segundos** (aunque no veas nada) → esto descarga los condensadores de la placa.
Como el botón está **en el teclado**, puede que el teclado esté fallando.  
Lo que hacemos los técnicos en ese caso es **puentear manualmente los pines de encendido** en la placa base. Con una **pinza metálica o destornillador**, hacé contacto breve entre esos dos pines (solo 1 segundo, sin fuerza). Si la notebook intenta encender → el teclado o el botón están fallando. Si no pasa nada hacemos otra cosa.

![[tecnico_05-vs-exo-r-001.png]]

Con un **multímetro**, medí el voltaje en el **conector de la batería** (debería dar alrededor de 7.4 V). Luego, probar continuidad en el **fusible de entrada (marcado con “F” en la placa)**. Si está abierto (sin continuidad), la placa no recibe energía hacia los circuitos internos.

**La batería está siendo utilizada como un “estabilizador” de energía.**  Cuando la batería está dañada o sin carga real, pero conectada, el circuito de la placa: intenta cargarla, no recibe retorno correcto, entra en protección, y la computadora se apaga o no vuelve a arrancar.
Cuando **desconectás la batería y la volvés a conectar**, el circuito del PMIC (Power Management IC): reinicia, borra la falla, y permite volver a encender la notebook.

Los cargadores baratos o débiles dan voltaje pero no entregan picos de corriente estables.  
En estos modelos, la batería actúa como **buffer** (almacena/compensa pequeños picos de energía).
❌ Como la batería está muerta, no compensa nada. El regulador (PMIC) trabaja al límite. 🔥 Después de horas, la placa entra en sobrecalentamiento o desbalance y se apaga.⛔ Y no reinicia hasta resetear la batería (desconectar y conectar).

Vimos si había componentes en corto en la placa, sacabamos conector de disco que estaba suelto y no se estaba utilizando. Volvimos a probar, dio imagen. La desconexion de la batería descargo los capacitores y borro de la placa el error de la placa. Tuvo un error porque la batería no funciona, cuando se satura al mantenerse conectada a corriente por bastante tiempo se vuelve a apagar. Además el microprocesador no tiene pasta térmica y puede estar aumentando temperatura. 




---
#### Referencias

