tipo::
tags::
fecha:: 2025-10-27
links::
links_externos::


### Problema con la contraseña

Cambiamos la contraseña y no recordamos la cuenta. Tuviemos que generar un hardUPDATE directamente desde la base de datos

##### Crear un usuario completamente nuevo

```sql
-- Primero eliminar el usuario existente (si existe)
DELETE FROM user WHERE id = 1;

-- Insertar nuevo usuario admin
INSERT INTO user (
  id, created_on, modified_on, is_deleted, is_disabled, 
  email, name, nickname, avatar, roles
) VALUES (
  1, 
  datetime('now'), 
  datetime('now'), 
  0, 
  0, 
  'admin@example.com', 
  'Administrator', 
  'admin', 
  '//www.gravatar.com/avatar/00000000000000000000000000000000?d=mm', 
  '["admin"]'
);
```

#### Tablas

```sql
.tables
```

#### Generar un nuevo hash para la nueva contraseña

```bash
# Instalar bcrypt para Python
apt-get update && apt-get install -y python3-pip
pip3 install bcrypt

# Generar hash con Python
python3 -c "
import bcrypt
password = b'tu_password_aqui'
hashed = bcrypt.hashpw(password, bcrypt.gensalt(12))
print(hashed.decode('utf-8'))
"
```

#### Cambiar pass

```sql
# **`password`**
UPDATE auth SET secret = '$2b$12$or9ObBBehv1mVb3rCtczg.U9HAi6xmTE.7oCBJLFrWHN5w2XsvKJ2' WHERE id = 1;
```

```bash
.quit

exit
docker restart nginx-proxy-manager
```



---
#### Referencias


