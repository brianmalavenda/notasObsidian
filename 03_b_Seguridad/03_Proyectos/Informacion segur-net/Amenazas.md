tipo::
tags:: #seguridad #red 
fecha:: 2026-08-25
links::
links_externos::

### Amenazas principales en un nodo aislado de internet

Encriptación (ransomware) y borrado son las más visibles, pero no son las únicas ni necesariamente las más peligrosas. Con el nodo sin internet, cambia el _vector_ pero no elimina el riesgo:

- **Corrupción silenciosa de datos (integridad, no disponibilidad)**: modificar sutilmente archivos de investigación sin borrarlos ni encriptarlos. Es más peligroso que el ransomware porque no se nota — podés estar trabajando meses sobre datos alterados sin saberlo. El ransomware al menos avisa que pasó algo; esto no.
- **Exfiltración diferida vía el mismo pendrive**: el malware no necesita internet para robar datos — puede copiar silenciosamente información del nodo _al propio pendrive_ (en una partición oculta o archivo camuflado) para que cuando esa memoria vuelva a tocar una máquina con internet, se filtre desde ahí. El aislamiento del nodo no protege si el vector de salida es el mismo medio físico que usás para entrar.
- **Backdoor/persistencia a la espera de que el nodo se reconecte alguna vez** (para actualizaciones, mantenimiento, etc.) — malware "dormido" que activa exfiltración o daño cuando detecta conectividad.
- **Ataques a nivel de firmware** (BadUSB): el payload vive en el controlador del pendrive, no en el sistema de archivos — esto es clave para tu segunda pregunta, ver abajo.
- **Contaminación cruzada entre los 5 nodos**: si el mismo pendrive (o uno de los estudiantes) circula entre distintos nodos aislados, cualquier infección salta de uno a otro, anulando el propósito de tener réplicas separadas.
- **Amenaza interna / social engineering**: alguien (alumno, docente, tercero) que introduce el archivo malicioso deliberadamente, disfrazado de material legítimo. No es un problema puramente técnico — hay que contemplarlo en el protocolo de acceso.

---
#### Referencias

