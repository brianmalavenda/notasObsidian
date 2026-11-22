tipo:: #servidor 
tags::
fecha:: 2025-10-20
links:: #servidor/guia_instalacion 
links_externos::
# ¿Qué es?

Es una identificación segura de que tu servidor cumple con los requisitos para establecer una comunicación a través del protocolo de encriptacion de datos SSL/TLS y que utiliza algún tipo de algoritmo para encriptar la comunicación a través de ese canal (protocolo). Esto asegura además que existe un identificador del servidor que es quien dice ser.

#### Analogía
1. Certificado SSL = Tu DNI/Pasaporte digital
2. Clave privada = Tu firma secreta (nunca la compartes)
3. Clave pública = La parte que sí puedes mostrar

En la Práctica:

🔒 CERTIFICADO (selfsigned.crt): 
- Es tu "credencial" que dice "soy quien digo ser"
- Lo envías a los visitantes para probar tu identidad

🔑 CLAVE PRIVADA (selfsigned.key):
- Es tu "firma secreta" que prueba que el certificado es tuyo
- NUNCA se comparte con nadie

## Como crear un certificado SSL

```bash
# Crear certificado con información de Argentina
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/selfsigned.key \
  -out /etc/nginx/ssl/selfsigned.crt \
  -subj "/C=AR/ST=Buenos_Aires/L=San_Martin/O=Home_Lab/CN=localhost"
```

* C=AR → Country = Argentina (código de 2 letras)
* ST=Buenos_Aires → State/Province = Buenos Aires
* L=San_Martin → Locality/City = San Martín
* O=Home_Lab → Organization = Home Lab (puedes poner tu nombre)
* CN=localhost → Common Name = localhost (Si tuviera un dominio publico adquirido podría poner ese dominio en vez de localhost)


---
#### Referencias

