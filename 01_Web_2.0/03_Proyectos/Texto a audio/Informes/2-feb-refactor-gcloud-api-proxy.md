tipo::
tags::
fecha:: 2026-02-02
links::
links_externos::

# Resumen Técnico: Implementación de Testing y Containerización en API Proxy TTS

## 📋 Información General

**Proyecto**: API Proxy para Google Cloud Text-to-Speech  
**Stack**: Python 3.11, Flask, pytest, Docker, Hatchling  
**Autor**: Documento generado para equipo de desarrollo  
**Fecha**: Febrero 2025

---

## 🎯 Problemas Encontrados y Soluciones

### 1. Estructura de Proyecto y Módulos Python

#### Problema: Nomenclatura de Carpetas

**Síntoma**: `ModuleNotFoundError: No module named 'api_proxy'`

**Causa Raíz**:

- Carpeta nombrada `api-proxy` (con guion) en lugar de `api_proxy` (con guion bajo)
    
- Python no permite guiones en nombres de módulos (PEP 8)
    

**Solución**:

bash

Copy

```bash
# Antes
api-proxy/                    # ❌ Inválido para imports

# Después  
api_proxy/                    # ✅ Válido
├── __init__.py
└── ...
```

**Lección**: Los nombres de paquetes Python deben seguir `snake_case`, nunca `kebab-case`.

---

### 2. Patrón de Diseño: Application Factory

#### Problema: Import Circular y Estado Global

**Síntoma**:

- Tests no podían crear instancias limpias de la app
    
- Configuración de Flask se ejecutaba al importar el módulo
    

**Causa Raíz**:

Python

Copy

```python
# ANTES (main.py) - Anti-patrón
from flask import Flask
app = Flask(__name__)         # Se ejecuta al importar

@app.route('/')
def home():
    pass
```

**Solución**: Application Factory Pattern

Python

Copy

```python
# DESPUÉS (__init__.py)
from flask import Flask

def create_app(config_name='development'):
    """Factory pattern: crea app bajo demanda"""
    app = Flask(__name__)
    app.config['TESTING'] = (config_name == 'testing')
    
    from . import routes
    routes.init_routes(app)
    return app

# Para compatibilidad
app = create_app()
```

**Beneficios**:

- Tests pueden crear `create_app('testing')` con configuración aislada
    
- No hay estado global compartido entre tests
    
- Permite múltiples instancias con diferentes configs
    

---

### 3. Imports Relativos vs Absolutos

#### Problema: `ModuleNotFoundError` al reestructurar

**Síntoma**:

Python

Copy

```python
from gcloud_tts.client import GoogleCloudTTSClient
# ModuleNotFoundError: No module named 'gcloud_tts'
```

**Análisis**: La estructura de imports cambió al mover `pyproject.toml`:

Copy

```
Antes (funcionaba):
api-proxy/
├── api_proxy/
│   └── routes.py  → from gcloud_tts...  # Relativo implícito
└── pyproject.toml     # En carpeta padre

Después (falla):
api_proxy/
├── api_proxy/
│   └── routes.py  → from gcloud_tts...  # ❌ No encuentra
├── pyproject.toml     # En misma carpeta
└── tests/
```

**Solución**: Imports relativos explícitos (PEP 328)

Python

Copy

```python
# EN routes.py (dentro del paquete api_proxy)
from .gcloud_tts.client import GoogleCloudTTSClient   # ✅ Relativo
from .utils.process_files import leer_docx_completo    # ✅ Relativo

# Alternativa: Absoluto desde raíz
from api_proxy.gcloud_tts.client import GoogleCloudTTSClient  # ✅ También válido
```

**Regla de Oro**:

- Dentro de un paquete: usar imports relativos (`.module`)
    
- Fuera del paquete (tests): usar imports absolutos (`package.module`)
    

---

### 4. Mocking en Tests Unitarios

#### Problema: Mock en ubicación incorrecta

**Síntoma**:

Python

Copy

```python
@patch('api_proxy.GoogleCloudTTSClient')  # ❌ Mal
# AttributeError: module 'api_proxy' has no attribute 'GoogleCloudTTSClient'
```

**Concepto Clave**: _Mock donde se USA, no donde se DEFINE_

**Diagrama de Flujo de Imports**:

Copy

```
gcloud_tts/client.py          # ← DEFINICIÓN de la clase
       ↓
api_proxy/routes.py           # ← IMPORT y USO: from .gcloud_tts import...
       ↓
tests/integration/test_api.py # ← MOCK debe apuntar aquí: api_proxy.routes
```

**Solución**:

Python

Copy

```python
# CORRECTO
@patch('api_proxy.routes.GoogleCloudTTSClient')  # ✅ Donde se importa
def test_endpoint(client, mock_gcloud):
    ...

# INCORRECTO
@patch('gcloud_tts.client.GoogleCloudTTSClient')  # ❌ Parcha definición, no uso
@patch('api_proxy.GoogleCloudTTSClient')          # ❌ No existe en ese namespace
```

**Fixture Reutilizable**:

Python

Copy

```python
@pytest.fixture
def mock_gcloud_client():
    with patch('api_proxy.routes.GoogleCloudTTSClient') as mock_class:
        mock_instance = Mock()
        mock_instance.is_authenticated = True
        mock_instance.synthesize_audio.return_value = Mock(
            audio_content=b"fake_audio"
        )
        mock_class.return_value = mock_instance
        yield mock_class
```

---

### 5. Build System: Hatchling Configuration

#### Problema: `ValueError: Unable to determine which files to ship`

**Síntoma**: Build falla porque hatchling no encuentra el paquete `api_proxy`

**Causa Raíz**:

- `pyproject.toml` no especifica explícitamente qué paquetes incluir
    
- Hatchling usa heurísticas que fallan con estructuras no estándar
    

**Solución**:

toml

Copy

```toml
[tool.hatch.build.targets.wheel]
packages = ["api_proxy"]  # ✅ Explícito

[tool.hatch.build]
include = [
    "api_proxy/**/*.py",   # ✅ Patrón inclusivo
    "run.py",              # ✅ Scripts adicionales si es necesario
]
exclude = [
    "tests/**/*",          # ❌ No incluir tests en la wheel
    ".gitignore",
    "__pycache__",
]
```

---

### 6. Docker y Entry Points

#### Problema: `ModuleNotFoundError` en contenedor

**Síntoma**:

bash

Copy

```bash
Traceback:
  File "/usr/local/bin/api-proxy", line 5, in <module>
    from api_proxy.__main__ import main
ModuleNotFoundError: No module named 'api_proxy'
```

**Causa Raíz**:

- Contexto de build incorrecto (ejecutando `docker build` desde raíz del repo en lugar de desde `api_proxy/`)
    
- O: `pip install -e .` no funcionaba porque faltaba `__main__.py`
    

**Solución**: Estructura correcta de entry points

**Archivo `api_proxy/__main__.py`** (requerido para `python -m`):

Python

Copy

```python
from api_proxy import create_app

def main():
    app = create_app('production')
    app.run(host='0.0.0.0', port=5000)

if __name__ == '__main__':
    main()
```

**Configuración en `pyproject.toml`**:

toml

Copy

```toml
[project.scripts]
api-proxy = "api_proxy.__main__:main"  # Comando CLI generado
```

**Dockerfile optimizado**:

dockerfile

Copy

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# 1. Copiar primero para aprovechar cache de capas
COPY pyproject.toml ./
COPY api_proxy/ ./api_proxy/

# 2. Instalar (NO editable en producción)
RUN pip install --no-cache-dir .

# 3. Usuario no-root por seguridad
RUN useradd -m -u 1000 appuser && chown -R appuser:appuser /app
USER appuser

EXPOSE 5000

# 4. Entry point usa el script generado por pip
CMD ["api-proxy"]
```

**Comandos de build**:

bash

Copy

```bash
# ✅ Correcto: desde la carpeta del servicio
cd api_proxy/
docker build -t api-proxy .

# ❌ Incorrecto: desde raíz del repo (contexto equivocado)
docker build -t api-proxy .  # Falla porque no encuentra api_proxy/
```

---

## 🏗️ Arquitectura Final

Copy

```
app-generar-audio-portal-diario-ejes/     # Monorepo
├── docker-compose.yml                      # Orquestación de servicios
├── front/                                  # React/Vue/Angular
├── back/                                   # API principal
└── api_proxy/                              # Servicio TTS (nuestro foco)
    ├── pyproject.toml                      # Build + dependencias + metadata
    ├── Dockerfile                          # Containerización
    ├── Makefile                            # Scripts de utilidad
    ├── README.md
    ├── api_proxy/                          # Paquete Python (src layout)
    │   ├── __init__.py                     # Factory: create_app()
    │   ├── __main__.py                     # Entry point: main()
    │   ├── routes.py                       # Endpoints Flask
    │   ├── gcloud_tts/                     # Cliente Google Cloud
    │   │   ├── __init__.py
    │   │   ├── client.py
    │   │   └── auth/
    │   └── utils/                          # Helpers
    │       ├── __init__.py
    │       ├── process_files.py
    │       └── validate.py
    └── tests/                              # Tests organizados
        ├── conftest.py                     # Fixtures compartidos
        ├── unit/
        │   ├── test_client.py              # Tests del cliente TTS
        │   └── test_synthesizer.py
        └── integration/
            └── test_api_endpoint.py        # Tests del endpoint Flask
```

---

## 🔧 Comandos de Referencia

### Desarrollo Local

bash

Copy

```bash
# Instalar en modo editable (desarrollo)
pip install -e ".[dev,test]"

# Ejecutar tests
pytest -v                           # Todos
pytest -m unit                      # Solo unitarios
pytest --cov=api_proxy --cov-report=html  # Con cobertura

# Limpiar cache
find . -type d -name "__pycache__" -exec rm -rf {} +
find . -type d -name ".pytest_cache" -exec rm -rf {} +

# Formatear código
black api_proxy/ tests/
ruff check api_proxy/ tests/
```

### Docker

bash

Copy

```bash
# Build
cd api_proxy/
docker build -t api-proxy:latest .

# Run con variables de entorno
docker run -p 5000:5000 \
  -v $(pwd)/secrets:/app/secrets:ro \
  -e GOOGLE_CREDENTIALS=/app/secrets/credentials.json \
  api-proxy:latest

# Compose (todos los servicios)
docker-compose up --build
```

---

## 📚 Principios Aplicados

Table

Copy

|Principio|Implementación|
|:--|:--|
|**Separation of Concerns**|`routes.py` (HTTP) separado de `client.py` (lógica de negocio)|
|**Dependency Injection**|Fixtures de pytest inyectan mocks|
|**Single Responsibility**|Cada módulo tiene una función clara|
|**Testability**|Application factory permite múltiples configs|
|**12-Factor App**|Config vía variables de entorno, contenedores stateless|

---

## 🎓 Checklist para Próximos Proyectos

- [ ] Nombres de carpetas: usar `snake_case`, nunca `kebab-case`
    
- [ ] Implementar Application Factory desde el día 1
    
- [ ] Usar imports relativos (`.module`) dentro de paquetes
    
- [ ] Configurar `tool.hatch.build.targets.wheel.packages` en `pyproject.toml`
    
- [ ] Crear `__main__.py` para ejecutar con `python -m package`
    
- [ ] Definir `[project.scripts]` para comandos CLI
    
- [ ] Estructurar tests con `conftest.py` y fixtures reutilizables
    
- [ ] Mockear donde se **usa**, no donde se **define**
    
- [ ] Construir Docker desde carpeta del servicio, no desde raíz del repo


---
#### Referencias

