tipo::
tags::
fecha:: 2026-08-19
links::
links_externos::

## Descargar librerías y probar con un archivo

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y ocrmypdf tesseract-ocr-spa ghostscript poppler-utils parallel unpaper

# Luego de la descarga de paquetes, verificar
ocrmypdf --version
tesseract --list-langs   # debe aparecer "spa"

#Una vez instalado los paquetes ya podemos probar con un archivo
ocrmypdf -l spa --deskew --rotate-pages --output-type pdf "libro_prueba.pdf" "libro_prueba_ocr.pdf"

```

Explicación de las flags clave:

- `-l spa`: idioma español. Para varios idiomas a la vez: `-l spa+eng`
- `--deskew`: endereza páginas levemente inclinadas (típico en escaneos)
- `--rotate-pages`: corrige páginas que se escanearon rotadas (90°, 180°)
- `--output-type pdf`: formato de salida (también puedes usar `pdfa` para archivo a largo plazo)

## Procesar todos los PDFs en lote

```bash
cd "/mnt/c/Usuarios/Pablo/Documentos/libros"
mkdir -p salida

# Procesa todos los PDFs, 2 archivos en paralelo (ajusta -j según tu CPU)
parallel --tag -j 2 ocrmypdf -l spa --deskew --rotate-pages '{}' 'salida/{}' ::: *.pdf
```

## Extraer el texto a .txt (paso intermedio hacia Word)

Los PDFs ya tienen la capa de texto, pero para abrirlos en Word y agrandar la fuente te conviene tener `.txt`. Usamos `pdftotext` (viene en `poppler-utils`):

```bash
mkdir -p texto
cd salida
for f in *.pdf; do
pdftotext -layout "$f" "../texto/${f%.pdf}.txt"
done
```

El flag `-layout` preserva la disposición de columnas y párrafos del original. Ahora puedes abrir cada `.txt` en Word, cambiar fuente a Arial 16, interlineado 1.5, e imprimir.

