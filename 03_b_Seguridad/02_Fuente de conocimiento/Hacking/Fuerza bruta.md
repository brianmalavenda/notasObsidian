tipo::
tags:: #web/seguridad 
fecha:: 2025-09-22
links::
links_externos::


Contra servidores para develar contraseñas de usuarios con alguna capa de nivel de seguridad que me permitan acceder hacia privilegios de root. 

Descargar la herramienta para linux llamada fail2ban
```
	apt-get install fail2ban
```

instalada en etc/fail2ban editamos el archivo de configuracion en /jail.local

```
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 5
bantime = 600
findtime = 600
```

iniciamos el servicio con 
```
systemctl start fail2ban
```

comprobamos con 

```
fail2ban-client status sshd
```

ahora, podemos hacer, desde una maquina atacante 

```
hydra -l kali -P rockyou.txt ssh://[IP SERVIDOR]
// esto es un archivo para hacer fuerza bruta
```

Me debería "bannear" y luego podría ver en los logs la información de la IP atacante


---
#### Referencias

