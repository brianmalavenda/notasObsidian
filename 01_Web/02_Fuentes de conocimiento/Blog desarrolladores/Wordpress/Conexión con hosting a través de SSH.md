
tags::
links: [[02_Configuracion]]
links_externos::

Usa este comando:
ssh-keygen -t rsa -b 4096 -C "tu_email@example.com"

- Usa rsa de 4096 bits.
- El comentario "tu_email@example.com" es opcional.
- Te preguntará:

- Dónde guardarla → presiona **Enter** para usar el default (~/.ssh/id_rsa)
- Passphrase → puedes poner una o dejarla en blanco para no usar contraseña.

---

**📂** **1. Agregarla al** **ssh-agent** **(en Linux/WSL)**

Primero, asegúrate de que el agente esté corriendo:

bash

CopyEdit

eval "$(ssh-agent -s)"

Luego agrega la clave:

bash

CopyEdit

ssh-add ~/.ssh/id_rsa

---

**🔁** **2. Copiar la clave pública al servidor remoto**

Usa ssh-copy-id si tienes acceso SSH:

bash

CopyEdit

ssh-copy-id usuario@tu-servidor.com

Si no, puedes copiar el contenido de tu clave pública manualmente:

bash

CopyEdit

cat ~/.ssh/id_rsa.pub

Y pegarlo en el archivo ~/.ssh/authorized_keys del servidor remoto.

**📂** **3. Copiar claves de WSl a Windows**

cp ~/.ssh/id_rsa /mnt/c/Users/brian/.ssh/id_rsa

cp ~/.ssh/id_rsa.pub /mnt/c/Users/brian/.ssh/id_rsa.pub

Permisos

chmod 600 ~/.ssh/id_rsa

chmod 644 ~/.ssh/id_rsa.pub

**4. Agregar clave ss hen el agente de Windows ssh**

ssh-add "C:\Users\brian\.ssh\id_rsa"

**❌** **¿No tienes acceso de root?**

Si estás en Bluehost con un plan compartido o restringido, probablemente **no puedas editar** **sshd_config** **ni reiniciar el servicio**. En ese caso:

- La **extensión Remote - SSH de VS Code no funcionará bien**, porque depende de esa funcionalidad.
- La única opción viable es:

- Usar **SFTP** (extensiones como _SFTP_ o _Remote Explorer_) para editar archivos.
- O trabajar con **WSL local + Git** y luego hacer git push/pull si usas repos.