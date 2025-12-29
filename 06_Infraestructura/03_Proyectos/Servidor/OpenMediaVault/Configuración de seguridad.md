tipo:: #servidor/config 
tags::
fecha:: 2025-12-23
links::
links_externos::
### Firewall

UFW (Uncomplicated Firewall) es un firewall para Linux diseñado para ser fácil de usar. Es básicamente una interfaz amigable para iptables (el firewall tradicional de Linux).

```bash
sudo ufw enable

# 1. Resetear si hay configuraciones previas (opcional)
sudo ufw --force reset

# 2. Denegar TODO el tráfico entrante por defecto
sudo ufw default deny incoming

# 3. Permitir TODO el tráfico saliente por defecto  
sudo ufw default allow outgoing

# 4. Permitir tus servicios (IMPORTANTE - haz esto EN ESTE ORDEN)
sudo ufw allow 2222/tcp comment 'SSH personalizado'
sudo ufw allow 80/tcp comment 'Servidor Web HTTP'
sudo ufw allow 443/tcp comment 'Servidor Web HTTPS'

# Ver todas las reglas
sudo ufw status numbered

# Ver con más detalle
sudo ufw show added
```

Probamos luego la seguridad

```bash
# Esto escanea los puertos de TU SERVIDOR desde DENTRO del mismo servidor
nmap 127.0.0.1
# **No prueba el firewall UFW** porque el tráfico local no pasa por el firewall.

# Esto escanea los puertos de TU PC CLIENTE desde el servidor
nmap 192.168.0.7
# Te muestra qué puertos están abiertos en tu **máquina cliente** vistos desde el servidor
```

### Consulta de puertos desde la ip pública desde otra red

1. Enrutamiento desde mi router. Crear reglas
2. Verificar que el servidor este escuchando los puertos que enruto desde el gateway (80,443)
3. Verificar que mi firewall permite acceder a estos puertos
4. Verificar que tengo una ip pública asignada por mi ISP

Salida de internet por una misma red que es el gateway de mi router
```bash
curl https://ippublica.net | grep "h3"
# si hago esta consulta de mi servidor web o mi pc cliente, mi ip publica es la misma
```

Desde fuera de mi red, por la WAN entonces busco ver el estado de los siguientes puertos
 ```bash
# Usa tu IP pública desde un cliente externo
nmap -p 80,443,2222,8080 [TU_IP_PUBLICA]
```

>[!NOTA]
curl https://www.ippublica.net/ | grep "h3"
con esto optengo la ip 181.46.57.233. Luego hago desde otrac omputadora cliente conectada a mi red wifi del telefono móvil, un 
nmap -p 80,443,2222,8080 181.46.57.233 y el resultado es Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 3.20 seconds

La IP pública no es accesible desde fuera porque estás detrás de un CGNAT o tu router no tiene la configuración correcta.

**Opción 1 (Más Probable): CGNAT**
1. Tu ISP te está compartiendo la IP pública con otros clientes
2. Común en ISPs residenciales (Movistar, Claro, etc.)
3. La IP "pública" que ves en realidad es compartida

**Opción 2: Router/Firewall del ISP**
1. Tu ISP bloquea los puertos entrantes
2. Necesitas "abrir puertos" en el router del ISP

**Opción 3: Tu Router Local**
1. No tienes configurado Port Forwarding


1.1 - No es el CGNAT
```bash
	curl ifconfig.me
	
	# la ip devuelta es 181.46.57.143 es igual que la obtenida por curl https://www.ippublica.net/ | grep "h3"
```
3.1
Desde el router intente agregar una regla para hacer un port-forwarding y en la nueva regla que quiero agregar no puedo agregar un puerto personalizado para mi conexion ssh

![[reglas_router_01.png]]

Despues igualmente cree 3 reglas para los puertos 80,8080 y 443

```bash
# En tu servidor, verifica qué puertos están escuchando
sudo netstat -tlnp | grep -E ':80|:443|:8080'

# Deberías ver algo como:
# tcp6   0   0 :::80      :::*       LISTEN
# tcp6   0   0 :::443     :::*       LISTEN  
# tcp6   0   0 :::8080    :::*       LISTEN
```

Comprobar servicios activos

```bash
# Verifica que tienes servicios escuchando
sudo systemctl status nginx
sudo systemctl status apache2
# o el servicio que uses en puerto 8080
```

Configurar nginx para abrir conexiones ssl en el puerto 443

```bash
# Para testing, podemos usar SSL auto-firmado
sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/selfsigned.key \
  -out /etc/nginx/ssl/selfsigned.crt \
  -subj "/C=ES/ST=Madrid/L=Madrid/O=IT/CN=localhost"

# Configurar Nginx para HTTPS
sudo tee /etc/nginx/sites-available/default << 'EOF'
server {
    listen 80;
    listen 443 ssl;
    server_name _;
    
    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;
    
    root /var/www/html;
    index index.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
EOF

# Recargar Nginx
sudo nginx -t
sudo systemctl reload nginx
```

en la primera que hice esto no tomó la configuracion porque en el servidor al instalar openmediavault ya genera una configuracion en nginx. Para ver que configuracion esta activa tenemos que hacer un:

```bash
# Verificar qué archivos de sitio están habilitados
sudo ls -la /etc/nginx/sites-enabled/
```

una vez que verificamos que no era la configuracion por default, modificamos el archivo que se llama openmediavault-webgui

ahí agregamos las siguientes líneas a la configuracion que tiene el sistema operativo
```bash
    listen 443 ssl default_server;
    listen [::]:443 ssl default_server;

    # SSL Configuration
    ssl_certificate /etc/nginx/ssl/selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/selfsigned.key;

    # Mejoras de seguridad SSL
    ssl_protocols TLSv1.2 TLSv1.3;

```

con esta configuracion estuvo ok

```
al Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:9122            0.0.0.0:*               LISTEN      679/sshd: /usr/sbin
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      431/systemd-resolve
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      8951/nginx: master
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      8951/nginx: master
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/init
tcp        0      0 0.0.0.0:5355            0.0.0.0:*               LISTEN      431/systemd-resolve
tcp        0      0 127.0.0.54:53           0.0.0.0:*               LISTEN      431/systemd-resolve
tcp6       0      0 :::9122                 :::*                    LISTEN      679/sshd: /usr/sbin
tcp6       0      0 :::443                  :::*                    LISTEN      8951/nginx: master
tcp6       0      0 :::80                   :::*                    LISTEN      8951/nginx: master
tcp6       0      0 :::111                  :::*                    LISTEN      1/init
tcp6       0      0 :::5355                 :::*                    LISTEN      431/systemd-resolve

```

luego quice agregar el algoritmo de cifrado por el que viajan los datos a través del protocolo SSL/TLS  y no funcionó:

>[!NOTA]
>1. Cifran los datos (los hacen ilegibles para terceros)
>2. Autentican la conexión (verifican que eres tú)
>3. Garantizan integridad (que nadie modificó los datos)


```bash
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;
    
    # 2025/10/20 22:49:24 [emerg] 10163#10163: SSL_CTX_set_cipher_list("ECDHE-AES256-GCM-SHA512:DHE-AES256-GCM-SHA512") failed (SSL: error:0A0000B9:SSL routines::no cipher match)
	# nginx: configuration file /etc/nginx/nginx.conf test failed

```

para corregir esto cambie el método de cifrado por ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-RSA-AES128-GCM-SHA256; y puse en on el cifrado 

### Desde un cliente

Con esta configuración ahora puedo hacer dos cosas, consultar los puertos abiertos del servidor desde el cliente y verificar que la conexión al puerto 443 esta abierta además de poder hacer una consulta al servidor por ese puerto y verificar que el servidor responde con la página por default del servicio nginx

```bash
	nmap 192.168.0.10
	# me contesta con el puerto 80 y 443 open
	curl -k -v https://192.168.0.10:443
	# status code 200, página de nginx
```

Bueno, ahora quisiera hacer esto pero pegarle a la ip publica y que me responda el servidor. Como hago.


---
#### Referencias

