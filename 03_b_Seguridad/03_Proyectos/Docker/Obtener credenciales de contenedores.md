tipo::
tags::
fecha:: 2026-02-06
links::
links_externos::


docker run --rm --entrypoint cat api_proxy:1.0.7 /app/secrets/gcloud_credentials.json | python3 -m json.tool | head -10

### **1. No almacenar credenciales en la imagen:**

dockerfile

# MAL: Credenciales en la imagen
COPY gcloud_credentials.json /app/secrets/

# BIEN: Montar secretos en runtime
# docker run -v /path/to/secrets:/app/secrets:ro ...

### **2. Usar secretos de Docker:**

bash

# Crear secreto
echo '{"api_key": "xxx"}' | docker secret create gcloud_credentials -

# Usar en servicio
docker service create --secret src=gcloud_credentials,target=/run/secrets/gcloud_credentials ...

### **3. Usar variables de entorno:**

dockerfile

# En Dockerfile
ENV GCLOUD_CREDENTIALS=""

# Al ejecutar
docker run -e GCLOUD_CREDENTIALS=$(cat credentials.json) ...

### **4. Eliminar credenciales de capas anteriores:**

dockerfile

# Esto NO funciona completamente porque las capas anteriores siguen accesibles
RUN rm /app/secrets/gcloud_credentials.json

# Mejor: usar multi-stage builds o no copiar nunca los secretos

### **5. Herramientas de seguridad:**

bash

# Escanear tu imagen con herramientas de seguridad
docker scan api_proxy:1.0.7

# Usar hadolint para revisar Dockerfile
hadolint Dockerfile

## **Prueba de vulnerabilidad en tu imagen:**

bash

# Ejecuta esto para ver qué credenciales están realmente en tu imagen:
docker history api_proxy:1.0.7
docker run --rm api_proxy:1.0.7 find / -name "*.json" -o -name "*.key" -o -name "*.pem" 2>/dev/null
---
#### Referencias

