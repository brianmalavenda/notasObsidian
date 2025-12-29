tipo:: #servidor/config
tags::
fecha:: 2025-12-23
links::
links_externos::


Creamos una llave publica en nuestra maquina cliente que usamos para administrar el server. 

```bash
ssh-keygen -t rsa -b 4096 "comentario" #creamos el par de llaves
ssh-copy-id [usuario]@[dominio-server] #copimos la llave publica a nuestro server
```

configurar la conexión SSH /etc/ssh/sshd_config

```bash
#AllowGroups root _ssh 
Port 9122
Protocol 2

# Verificar sobre todo el puerto que quedó asignado
sudo grep -i port /etc/ssh/sshd_config
```

```bash
# Opción 1: Recargar configuración
sudo systemctl reload ssh

# Opción 2: Reiniciar completamente
sudo systemctl restart ssh

# Opción 3: Si lo anterior no funciona
sudo systemctl stop ssh
sudo systemctl start ssh

# podemos ver los puertos abiertos
nmap -sT -O localhost

```

Con el cambio de puerto para conectarme al servidor tengo que escribir

```bash

ssh -p 9122 root@srv-sanmartin901

```



## Nuevo acceso


Creamos nuevamente una credencial ssh para acceder con el usuario admin-ssh

```bash
ssh-keygen -t ed25519 -C "admin-ssh@sanmartinici.com.ar" -f ~/.ssh/srv-sanmartin901

# ssh -i ~/.ssh/srv-sm901 admin-ssh@192.168.0.10 -p 1122

# Copiar la clave publica de mi pc al servidor
ssh-copy-id -i ~/.ssh/srv-sanmartin901.pub -p 9122 root@srv-sanmartin901.local
# parado en la carpeta ssh de mi pc admin con la clave srv-sanmartin901.pub creada
# -i indicada donde voy a copiar mi clave publica en el servidor
# antes tuve que habilitar poder conectarme como root al server
# agregue el contenido de mi clave pub en authorized_keys

#Acceder como root
ssh -i ~/.ssh/srv-sanmartin901 root@srv-sanmartin901.local -p 9122
```


# Solución

Primer creo el usuario con el que voy a conectarme. Lo hago a través de la UI de OMV y lo agrego al grupo de _ssh, sudo, admin, openmediavault_admin,users_

Luego, en mi pc admin cliente creo las llaves publico-privadas para el acceso ssh

```bash
# aca a mi clave le puse el nombre de srv-sanmartin901
ssh-keygen -t ed25519 -C "admin-ssh@sanmartinici.com.ar" -f ~/.ssh/srv-sanmartin901
```

Una vez echo esto copio mi  llave al servidor, lo hago para el usuario que tiene el acceso

```bash
# le pongo usuario root aca
ssh-copy-id -i ~/.ssh/srv-sanmartin901.pub -p 9122 root@srv-sanmartin901.local
```

Luego de esto, como el nombre de la clave no es un "por default", es decir: `d_rsa`, `id_ed25519`, `id_ecdsa`. Entonces puedo, de manera sencilla, crear un archivo de configuracion en `~/.ssh/config` y a este archivo le indico que llave tiene que usar para conectarse a que servidor y a través de que puerto y con que usuario. Por lo tanto, el archivo tendrías este contenido

```bash
Host srv-sanmartin901
    HostName srv-sanmartin901.local
    User root
    Port 9122
    IdentityFile ~/.ssh/srv-sanmartin901
    IdentitiesOnly yes  # Solo prueba esta clave
```

Hecho esto, ahora me puedo conectar con esta simple línea `ssh srv-sanmartin901`

Si quiero evitar este paso, necesariamente cada vez que acceda voy a tener que indicar manualmente que la clave de acceso se llama srv-sanmartin901 de esta manera

```bash
#Acceder como root
ssh -i ~/.ssh/srv-sanmartin901 root@srv-sanmartin901.local -p 9122
```


---
#### Referencias

