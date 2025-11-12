tipo::
tags::
fecha:: 2025-11-12
links::
links_externos::

Comandos básicos

```bash
git config

git remote add origin

git add . # guardo todos los cambios en el stage
git commit -m "[comentario]" # hago un commit (aprobacion) de los cambios
git push # envio los cambios a mi repositorio remoto

git checkout -b "[nombre de la rama]" # creo una rama
git checkout . # descarto todos los cambios, podría reemplazar el punto por el nombre puntual de un archivo
git checkout -- [nombre de archivo] # descarta los cambios locales en el archivo

# esto elimina el archivo del tracking pero lo mantiene localmente. 
git rm --cached .obsidian/workspace.json
# Si el archivo ya está commitado en el repositorio remoto agregar
git commit -m "Stop tracking workspace.json"
```


---
#### Referencias

