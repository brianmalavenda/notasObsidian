tipo:: #web/desarrollo
tags::
fecha:: 2025-09-12
links::
links_externos::

* Interpretado
* Alto nivel
* Propósito general
* Multiparadigma

# Paquetes 

**Reutilización y Modularidad**
```python
# SIN paquete (código espagueti)
mi_proyecto/
├── script1.py  # Tiene funciones de auth
├── script2.py  # También tiene funciones de auth
├── script3.py  # Y aquí también...
└── main.py     # Importa de todos lados

# CON paquete (organizado)
mi_proyecto/
├── gcloud_tts/          # Tu paquete
│   ├── auth/            # Módulo de autenticación
│   ├── tts/             # Módulo de síntesis
│   └── utils/           # Utilidades comunes
├── tests/               # Tests separados
├── examples/            # Ejemplos de uso
└── main.py              # Usa tu paquete limpiamante
```

**Gestión de dependencias**

```python
# setup.py o pyproject.toml
# Define claramente qué necesita tu paquete

# pyproject.toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]

[project]
name = "gcloud-tts"
version = "1.0.0"
dependencies = [
    "google-cloud-texttospeech>=2.0.0",
    "pydantic>=2.0.0",
    "cryptography>=3.4.0"
]

# Instalación limpia
pip install -e .                # Instala en modo desarrollo
pip install gcloud-tts          # Distribución futura
```

**Distribución y compartición**

```python
# Puedes publicar en PyPI
python -m build                 # Crea distribución
twine upload dist/*            # Sube a PyPI

# Otros pueden instalarlo fácilmente
pip install gcloud-tts

# O usar desde GitHub
pip install git+https://github.com/tu-usuario/gcloud-tts.git
```

**Control de versiones y mantenibilidad**

```python
# __init__.py
__version__ = "1.2.0"
__author__ = "Tu Nombre"
__license__ = "MIT"

# SemVer claro
# 1.2.0 -> MAJOR.MINOR.PATCH
# - MAJOR: Cambios incompatibles
# - MINOR: Nuevas funcionalidades compatibles  
# - PATCH: Correcciones de bugs

# Changelog organizado
CHANGELOG.md:
- v1.2.0: Added batch synthesis
- v1.1.0: Added SSML support
- v1.0.0: Initial release
```

## `__init__.py`

**a) Marcar un directorio como paquete Python**

```python
# Con __init__.py
proyecto/
├── auth/
│   ├── __init__.py      # ✅ AHORA es un paquete
│   └── authenticator.py
└── main.py
```

**b) Controlar qué se exporta (API pública)**

```python
# auth/__init__.py

# Opción 1: Exportar selectivamente
from .authenticator import GoogleCloudAuthenticator
from .credentials import GoogleCloudCredentials

__all__ = ['GoogleCloudAuthenticator', 'GoogleCloudCredentials']

# Opción 2: Exportar todo automáticamente
# (menos recomendado, pierdes control)
```


## `_post_init__`


Las dataclasses automáticamente generan: `__init__(), __repr__() ,__eq__()`, Pero **no validan automáticamente**. Aquí entra `__post_init__`:

```python
from dataclasses import dataclass

# EJEMPLO BÁSICO
@dataclass
class Persona:
    nombre: str
    edad: int
    
    def __post_init__(self):
        # Se ejecuta DESPUÉS de __init__
        if self.edad < 0:
            raise ValueError("La edad no puede ser negativa")
        if not self.nombre:
            raise ValueError("El nombre es requerido")

# USO
p1 = Persona("Juan", 25)  # ✅ OK
p2 = Persona("", 25)      # ❌ ValueError: El nombre es requerido
p3 = Persona("Ana", -5)   # ❌ ValueError: La edad no puede ser negativa
```

+ Validación centralizada de datos
+ Transformación de datos después de la inicialización
+ Cálculo de campos derivados
+ Mantiene la inmutabilidad de la dataclass

# Patrón fachada o facade pattern

* Abstracción: Oculta complejidad interna
* Consistencia: Misma interfaz para todas las operaciones
* Mantenibilidad: Cambios internos no afectan a usuarios
* Testeabilidad: Se puede mockear fácilmente
* Extensibilidad: Fácil añadir nuevas funcionalidades

```python
# SIN Facade (complejo para el usuario)
from auth.authenticator import GoogleCloudAuthenticator
from auth.credentials import GoogleCloudCredentials
from tts.synthesizer import TextToSpeechSynthesizer

# El usuario tiene que hacer TODO
creds = GoogleCloudCredentials(project_id="...", ...)
auth = GoogleCloudAuthenticator(creds)
tts = TextToSpeechSynthesizer(auth)
audio = tts.synthesize("texto")

# CON Facade (sencillo para el usuario)
from gcloud_tts import GoogleCloudTTSClient

# Una sola línea para el usuario
client = GoogleCloudTTSClient(project_id="...")
audio = client.synthesize_audio("texto")
```

a) Simplifica la API para el usuario final
b) Coordina componentes internos
c) Proporciona una interfaz unificada
d) Maneja el ciclo de vida

```python
class GoogleCloudTTSClient:
    def __enter__(self):
        """Para usar con context manager"""
        self.authenticator.authenticate()
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        """Limpieza de recursos"""
        self._cleanup()
    
    def _cleanup(self):
        """Cierra conexiones, libera recursos"""
        if hasattr(self.tts, '_client'):
            self.tts._client.close()
```
