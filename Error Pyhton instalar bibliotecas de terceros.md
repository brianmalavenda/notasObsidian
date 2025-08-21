
> error: externally-managed-environment × This environment is externally managed ╰─> To install Python packages system-wide, try apt install python3-xyz, where xyz is the package you are trying to install.

Este error ocurre porque en las versiones recientes de **Debian/Ubuntu** (y otras distribuciones que usan Python 3.11+), el sistema operativo bloquea la instalación de paquetes Python con `pip` a nivel global para evitar conflictos con los paquetes manejados por el gestor de paquetes (`apt`).

---
 ### **0. Actualizar el sistema**:
sudo apt update && sudo apt upgrade -y
sudo apt install python3-venv python3-pip

---
 ### **1. Crea un entorno virtual** (aislado del sistema):
python3 -m venv ~/entorno_python  # Crea el entorno en tu HOME
source ~/entorno_python/bin/activate  # Actívalo (Linux/macOS)

---
 ### **2. Instala las bibliotecas dentro del entorno virtual**:
pip install shapely pyproj  # Ahora sí funcionará sin errores

---
 ### **3. Ejecuta tu script**:
python3 test-library.py  # Asegúrate de que el entorno esté activado

---
 ### **4. Salir de un entorno virtual de Pyhton:**
 Utilizar el comando 
	 `deactivate`

---
**5. Reactivar el entorno**:  
    Si necesitas volver a usarlo, ejecuta:
    source ~/venv_geo/bin/activate
    
---
**6. Eliminar el entorno virtual** (opcional):  
    Si ya no lo necesitas, borra la carpeta del entorno:    
    rm -rf ~/venv_geo 