tipo::
tags::
fecha:: 2026-02-07
links::
links_externos::

Este error es muy común cuando trabajas con **VS Code** dentro de un contenedor o vía SSH. El socket `.sock` que mencionas es el "puente" que usa VS Code para pasarle tus credenciales de GitHub al comando `git` en la terminal. Cuando dice `ECONNREFUSED`, ese puente se ha roto.

---
#### Referencias

