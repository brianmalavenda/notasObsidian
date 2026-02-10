tipo::
tags::
fecha:: 2026-01-27
links::
links_externos::

### Creamos la clave

```bash
# Iniciamos el agente
eval "$(ssh-agent -s)"
```

```bash
# Creamos la clave
ssh-keygen -t ed25519 -C "tu_email@ejemplo.com"
o
ssh-keygen -t ed25519

# Agregar la clave
ssh-add ~/.ssh/id_ed25519

ssh-keygen -t ed25519 -C "tu_email@ejemplo.com" -f ~/.ssh/github_personal
# Se crearán dos archivos: `github_personal` (clave privada) y `github_personal.pub` (clave pública)
# Agregar la clave
ssh-add ~/.ssh/github_personal
```

```bash
# Copiamos la clave publica 
cat ~/.ssh/id_ed25519.pub

o

cat ~/.ssh/github_personal.pub
```

### Agregar la clave SSH a GitHub

1. Ve a [GitHub.com](https://github.com/)
2. Click en tu perfil → **Settings**
3. **SSH and GPG keys** → **New SSH key**    
4. Pega la clave pública y guarda

```bash
# Verifica la URL actual
git remote -v

# Cambia la URL a SSH
git remote set-url origin git@github.com:brianmalavenda/app-generar-audio-portal-diario-ejes.git
```

### Probamos la conexión

```bash
ssh -T git@github.com
```

realizar un push a la rama
git push origin main


# Si no funciona, vamos a revisar!

El archivo de configuración que le indica a git que llave usar esta en config, si hemos puesto un nombre personalizado a la llave entonces debemos revisar ahí para indicarle específicamente la llave que debe buscar

```bash
nano ~/.ssh/config
```

Acá hay que asegurarse de que tenga este contenido (esto le dice a SSH: "Cuando vayas a GitHub, usa esta llave específica"):

```text
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/adm-brian
    IdentitiesOnly yes
```

SSH es extremadamente exigente. Si tu llave privada tiene permisos que permiten que otros usuarios la lean, SSH la ignora por seguridad. Hay que ejecutar esto para asegurar que solo `root` pueda verla:

```bash
chmod 600 ~/.ssh/adm-brian
chmod 644 ~/.ssh/adm-brian.pub
```


---
#### Referencias

