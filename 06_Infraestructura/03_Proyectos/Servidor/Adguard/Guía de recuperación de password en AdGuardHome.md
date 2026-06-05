tipo::
tags::
fecha:: 2026-05-14
links::
links_externos::

### Fase 1: Generar el Hash de la nueva contraseña

#### Opción A: Usando Python (Recomendado por compatibilidad)

Como AdGuard usa **BCrypt**, podemos generar el hash directamente desde la terminal de tu LXC si tienes Python instalado (casi todos los Linux lo traen).

Ejecuta este comando (reemplaza `tu_nueva_clave` por la que desees):

```bash
python3 -c 'import bcrypt; print(bcrypt.hashpw(b"tu_nueva_clave", bcrypt.gensalt(log_rounds=10)).decode())'
```

- **Nota:** Si te dice que no encuentra el módulo `bcrypt`, puedes instalarlo rápido con `pip install bcrypt` o usar la Opción B.
    
- **Resultado:** Obtendrás algo como `$2b$10$R9h/cIPz...`. **Cópialo íntegro.**
    

#### Opción B: Usando el binario de AdGuard

Si prefieres usar el propio contenedor para asegurar que el algoritmo sea el exacto de su versión:


```bash
docker run --rm adguard/adguardhome /opt/adguardhome/AdGuardHome --hash-password "tu_nueva_clave"
```

---

### Fase 2: Localizar el archivo de configuración en el Host

No entres al contenedor. Busca el archivo directamente en tu máquina `jmrosas` para tener acceso a `nano`:

```bash
find / -name AdGuardHome.yaml 2>/dev/null
```

_Anota la ruta (ej: `/var/lib/docker/volumes/adguard_conf/_data/AdGuardHome.yaml`)._

---

### Fase 3: Aplicar el cambio

Sigue este orden estricto para evitar que Docker pise tus cambios:

1. **Detén el contenedor:**
    
    ```bash
    docker stop adguard
    ```
    
2. **Edita el archivo con nano:**
    

```bash
    nano /RUTA/QUE/ENCONTRASTE/AdGuardHome.yaml
```

3.  **Busca la sección `users`:**
    Usa `Ctrl + W` para buscar la palabra "users". Verás algo así:
    
```yaml
    users:
      - name: admin  <-- Verifica tu nombre de usuario aquí
        password: "$2a$10$HASH_ANTIGUO_AQUI"
```

4.  **Reemplaza el hash:**
    Borra el hash viejo y pega el que generaste con Python o con el comando de Docker (asegúrate de que quede entre comillas dobles).

5.  **Guarda y sal:**
    `Ctrl + O`, `Enter`, `Ctrl + X`.

---

### Fase 4: Reinicio y Verificación

Vuelve a levantar el servicio:
```bash
docker start adguard
```

Ahora intenta acceder a la IP de tu LXC por el puerto habitual (normalmente el 80 o el 3000 si estás en la configuración inicial).

> [!TIP] **¿Por qué Python es mejor?** A veces los argumentos de los binarios cambian entre versiones (como te pasó con el `-p`), pero el algoritmo **BCrypt** es un estándar. Al generarlo con Python, te aseguras de que el formato sea siempre el correcto independientemente de cómo se comporte el ejecutable de AdGuard ese día.


---
#### Referencias

