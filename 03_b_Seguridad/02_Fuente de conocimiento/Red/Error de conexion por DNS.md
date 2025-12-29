tipo::
tags::
fecha:: 2025-10-17
links::
links_externos::

## Caso 1: Youtube no carga los videos o las paginas por https alertan que la conexión no es privada

Se puede hacer una prueba desde Powershell para ver si desde el servidor DNS por el cual estamos intentando navegar tiene problemas

```powershell
PS C:\WINDOWS\system32> nslookup youtube.com 180.76.76.76
Servidor:  public-dns-a.baidu.com
Address:  180.76.76.76

Respuesta no autoritativa:
Nombre:  youtube.com
Addresses:  2001::1
          31.13.73.9

PS C:\WINDOWS\system32> nslookup youtube.com 9.9.9.9
Servidor:  dns9.quad9.net
Address:  9.9.9.9

Respuesta no autoritativa:
Nombre:  youtube.com
Addresses:  2800:3f0:4003:c00::be
          2800:3f0:4003:c00::88
          2800:3f0:4003:c00::5b
          2800:3f0:4003:c00::5d
          64.233.186.93
          64.233.186.190
          64.233.186.136
          64.233.186.91
```



---
#### Referencias

