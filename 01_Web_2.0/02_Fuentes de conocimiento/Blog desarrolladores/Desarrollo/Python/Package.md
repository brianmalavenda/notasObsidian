tipo::
tags::
fecha:: 2026-02-06
links::
links_externos::


##  Identificar Paquetes Instalados

```bash
# Listar todos los paquetes instalados en tu entorno
pip list

# Filtrar los tuyos (los que tienen tu ruta local)
pip list | grep -E "(api|proxy|gcloud)"

# Ver detalles de un paquete específico
pip show api-proxy
pip show gcloud-tts
pip show gcloud_tts

# Ver qué archivos instaló un paquete (útil para saber dónde está)
pip show -f api-proxy 2>/dev/null || pip show -f gcloud_tts 2>/dev/null
```

## 2️⃣ Borrar TODO (Limpieza Completa)

```bash
# Desinstalar todos tus paquetes (incluyendo variaciones de nombre)
pip uninstall api-proxy api_proxy gcloud-tts gcloud_tts -y 2>/dev/null || true

# Limpiar caché de pip
pip cache purge

# Limpiar build artifacts de tu proyecto
cd /home/brian/Repositorio/app-generar-audio-portal-diario-ejes/api_proxy

rm -rf build/
rm -rf dist/
rm -rf *.egg-info/
rm -rf api_proxy.egg-info/
rm -rf gcloud_tts.egg-info/
rm -rf api_proxy/gcloud_tts.egg-info/

# Limpiar __pycache__ y .pyc files
find . -type d -name __pycache__ -exec rm -rf {} + 2>/dev/null || true
find . -type f -name "*.pyc" -delete 2>/dev/null || true

# Verificar que no quedó nada
pip list | grep -E "(api|proxy|gcloud)"
```

---
#### Referencias

