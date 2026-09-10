tipo::
tags::
fecha:: 2026-09-08
links::
links_externos::

### 1. Conversión de Formato de Iconos (ICO a PNG/SVG)

Las especificaciones del motor de renderizado de temas de iconos en FreeDesktop (`hicolor-icon-theme` / GTK) **no soportan de forma nativa ficheros con contenedor `.ico` de Microsoft Windows** para renderizado de accesos directos del sistema. Se requiere la conversión a mapa de bits PNG o gráfico vectorial SVG.

Utilizando la suite `ImageMagick`:
```bash
# Instalación de dependencias (si aplica)
sudo apt update && sudo apt install -y imagemagick

# Conversión y extracción del Layer de mayor resolución del .ico
convert input_logo.ico[0] ~/.local/share/icons/app-custom-icon.png
```

### 2. Estructura del Fichero `.desktop`

Crea el archivo ejecutable en la ruta correspondiente del entorno de usuario:

```bash
nano ~/.local/share/applications/custom-app.desktop
```

Estructura de metadatos estandarizada:

```TOML
[Desktop Entry]
Version=1.0
Type=Application
Name=Servicio Web Personalizado
Comment=Acceso directo parametrizado a aplicación Web
Exec=x-www-browser --new-window [https://midominio.com](https://midominio.com)
Icon=app-custom-icon
Terminal=false
Categories=Network;WebBrowser;
StartupNotify=true
```

> **Nota sobre el parámetro `Icon`:** Si el archivo se encuentra dentro de `~/.local/share/icons/` registrado bajo el nombre `app-custom-icon.png`, la especificación permite omitir la extensión `.png` y la ruta absoluta, permitiendo que el demonio de temas del entorno de escritorio (GNOME/XFCE/KDE) lo resuelva automáticamente mediante el subsistema XDG.

### 3. Asignación de Permisos de Ejecución

Para garantizar que los entornos de escritorio como GNOME Shell (en Debian 12) validen la confianza del lanzador:

```bash
chmod +x ~/.local/share/applications/custom-app.desktop
update-desktop-database ~/.local/share/applications/
```

## 5. Conclusiones

- La colisión entre tipos de archivo e inodos en directorios reservadas de `$HOME` impide la correcta resolución de rutas de recursos para los entornos gráficos.
    
- La adecuada estructuración dentro de `~/.local/share/icons/` garantiza la portabilidad y coherencia estética de las _Desktop Entries_ sin recurrir a elevación de privilegios (`sudo`) ni alterar los directorios globales del sistema (`/usr/share/icons/`).

---
#### Referencias

