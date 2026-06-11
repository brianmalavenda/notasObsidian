tipo::
tags::
fecha:: 2025-09-22
links::
links_externos::
#desarrollo/python

ERROR
```
INFO: pip is looking at multiple versions of pydantic to determine which version is compatible with other requirements. This could take a while.
ERROR: Cannot install -r requirements.txt (line 19) and pydantic-core==2.14.0 because these package versions have conflicting dependencies.

The conflict is caused by:
    The user requested pydantic-core==2.14.0
    pydantic 2.5.0 depends on pydantic-core==2.14.1

To fix this you could try to:
1. loosen the range of package versions you've specified
2. remove package versions to allow pip attempt to solve the dependency conflict

ERROR: ResolutionImpossible: for help visit https://pip.pypa.io/en/latest/topics/dependency-resolution/#dealing-with-dependency-conflicts

```

RESOLUCION

```
# Limpiar instalación previa (si es necesario)
pip uninstall pydantic pydantic-core -y

# Instalar con resolución moderna de dependencias
pip install --upgrade pip
pip install -r requirements.txt

# O si persisten problemas, forzar reinstalación
pip install --force-reinstall -r requirements.txt

```

---
#### Referencias

