tipo:: [[Seguridad Red]]
tags:: #seguridad/web/vpn #seguridad/red/vpn #vpn  
fecha:: 2025-09-06
links:: [[Proton VPN]] [[Crear cuenta proton]] [[03_b_Seguridad/02_Fuente de conocimiento/VPN]]
links_externos::

### ¿Qué es una VPN?

Una **VPN (Red Privada Virtual)** es una tecnología que crea un túnel cifrado entre tu dispositivo y un servidor remoto operado por un servicio de VPN. Todo el tráfico de internet pasa a través de este túnel, lo que significa que:

- Tu **dirección IP real** queda oculta y se reemplaza por la del servidor VPN.
    
- Tus datos se **cifran** mediante protocolos de seguridad (como WireGuard, OpenVPN o IKEv2), evitando que terceros (proveedores de internet, gobiernos, ciberdelincuentes) intercepten tu actividad en línea.
    
### Funcionamiento técnico clave para privacidad y anonimato:

1. **Cifrado de datos**:
    - Los protocolos de cifrado (ej. AES-256) convierten tus datos en un formato ilegible durante la transmisión. Solo el servidor VPN puede descifrarlos con una clave segura.
2. **Ocultamiento de la dirección IP**:
    - Tu dirección IP (identificador único en internet) se enmascara con la IP del servidor VPN. Esto dificulta que sitios web, servicios o adversarios rastreen tu ubicación real o identifiquen tu dispositivo.
3. **Prevención de fugas de DNS**:
    - Una VPN confiable fuerza las solicitudes de DNS (que traducen nombres de dominio a direcciones IP) a través de su túnel cifrado, evitando que tu proveedor de internet vea qué sitios visitas.
4. **Política de no registro (No-Logs)**:
    - Servicios como Proton VPN operan bajo una **política estricta de no almacenar registros** de tu actividad, conexiones o direcciones IP originales. Esto significa que, incluso bajo solicitud legal, no pueden compartir datos que no existen.


