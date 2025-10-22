tipo::
tags::
fecha:: 2025-10-16
links::
links_externos::



### Análisis de espectro de frecuencias inalámbricas de la zona

#### Frecuencia de 2,4 GHz

![[Pasted image 20251016072305.png]]

Mi red es Telecentro-fa71 y esta configurada en el canal 9

* Eje X → el canal Wi-Fi (del 1 al 13).
* Eje Y → la intensidad de señal (dBm), donde valores más altos (más cerca de 0) significan señales más fuertes.

Las “campanas” se superponen cuando dos o más redes usan canales solapados, lo que genera interferencias. Las señales se solapan entre los canales 1–3 y 9–13.
La red Telecentro-c8e6 (canal 11 aprox.) tiene una señal bastante fuerte y su “campana” invade parte del espectro de tu red (si la tuya está cerca del canal 11 también).

1- Canal 1–3: hay varias redes (Telecentro-fa71, ef93, TP-LINK).
2- Canal 6: solo NetwayFibra-483E.
3- Canal 11: Telecentro-c8e6 y Speedy-Fibra (dos redes con buena potencia).

Una señal fuerte por ejemplo es Telecentro-c8e6 en el canal 11, pero quizas el solapamiento en el canal 1 de varias señales débiles no sea grabe. Hay que probar estabilidad (perdida de paquetes haciendo un ping a 8.8.8.8 y/o al gateway) y velocidad (haciendo un test de velocidad en https://fast.com/es/# o https://speed-test.es/)


#### Wifi test

Notebook
5 ghz, canal 149

**página fast.com**
bajada 300 mbps
carga 46 mbps


---
#### Referencias

