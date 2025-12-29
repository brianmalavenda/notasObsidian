tipo:: #servidor/web
tags::
fecha:: 2025-11-14
links::
links_externos::

El login POST se procesa inmediatamente (303 redirect), pero luego **el cliente tarda 4 minutos en seguir la redirección**.

### **1. Problema de DNS en el cliente:**

```bash
# En el cliente Windows, verificar DNS
nslookup cloud.sanmartinici.com.ar

# Probar con IP directamente
# Editar hosts temporalmente: 181.46.57.143 cloud.sanmartinici.com.ar

```


---
#### Referencias

