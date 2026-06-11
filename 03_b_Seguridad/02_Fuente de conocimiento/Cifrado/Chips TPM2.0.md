tipo::
tags:: #privacidad/dispositivo 
fecha:: 2025-11-06
links::
links_externos::

![[Pasted image 20251106165741.png]]

TPM significa **Trusted Platform Module** (Módulo de Plataforma Confiable). Es un **chip criptográfico** diseñado para proporcionar funciones relacionadas con la seguridad a nivel de hardware.

- **Función Principal:** Su principal tarea es **almacenar de forma segura claves de cifrado**, certificados digitales y contraseñas de manera que no puedan ser leídas por software malicioso ni por ataques físicos simples.
    
- **Actúa como una caja fuerte digital** dentro de tu PC, blindando la información más sensible de tu sistema.

El TPM es esencial para las funciones de seguridad modernas de los sistemas operativos y el hardware:

- **Cifrado de Disco (BitLocker):** El TPM almacena la clave de cifrado de BitLocker (la herramienta de cifrado de disco de Windows). Esto significa que tu disco duro está cifrado y solo puede ser desbloqueado cuando el TPM verifica que el hardware del sistema (placa madre, BIOS) no ha sido alterado.
    
- **Seguridad de Arranque (Secure Boot):** Ayuda a garantizar que el proceso de arranque del sistema operativo sea confiable y que no se hayan inyectado _malware_ en las etapas iniciales de inicio.
    
- **Windows 11:** El TPM 2.0 es un **requisito de hardware** obligatorio para instalar Windows 11, ya que Microsoft aumentó significativamente el estándar de seguridad de su sistema operativo.

---
#### Referencias
