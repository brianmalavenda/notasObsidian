tipo::
tags::
fecha:: 2026-07-30
links::
links_externos::

1. **Reemplaza los repositorios a Trixie:**
    
    ```Bash
    sed -i 's/bookworm/trixie/g' /etc/apt/sources.list
    sed -i 's/bookworm/trixie/g' /etc/apt/sources.list.d/*.list 2>/dev/null || true
    ```
    
2. **Actualiza la lista de paquetes y realiza el upgrade del sistema:**
    
    ```Bash
    apt update
    apt dist-upgrade -y
    ```
    
1. **Limpia los paquetes obsoletos y reinicia el LXC:**
    
    ```Bash
    apt autoremove --purge -y
    reboot
	```

---
#### Referencias

