tipo::
tags::
fecha:: 2025-09-30
links::
links_externos::


### Error 0001

```
wsl: Failed to start the systemd user session for 'brian'. See journalctl for more details. Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.6.87.2-microsoft-standard-WSL2 x86_64) * Documentation: https://help.ubuntu.com * Management: https://landscape.canonical.com * Support: https://ubuntu.com/pro
```

saqué de mi **sudo nano /etc/wsl.conf**
```
[user]
default=brian
```

y dejé solo
```
[boot]
systemd=true
```

apaga mi wsl con ```wsl --shutdown``` y volví a prenderlo ```wsl```, pero no funcionó. Volví a tener el "mismo error"



```
# Actualizar e instalar paquetes críticos
sudo apt update
sudo apt install --reinstall tzdata dbus systemd pam
```

sacamos package **pam** porque daba error

  ```
root@brian:/mnt/c/WINDOWS/system32# sudo apt install --reinstall tzdata dbus systemd pam
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
E: Unable to locate package pam
  ```

verificamos que quedó todo OK y avanzamos con lo siguiente

```
# Reiniciar D-Bus
sudo systemctl enable dbus
sudo systemctl start dbus

# Verificar que esté funcionando
systemctl status dbus
```



```
# Reinstalar PAM
sudo apt install --reinstall libpam-modules libpam-runtime

# Verificar que el módulo esté presente
ls -la /usr/lib/security/pam_lastlog.so
```

---
#### Referencias

