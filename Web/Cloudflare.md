https://dash.cloudflare.com/cbc9f7d6264da907dce7d15c3546c05b/famosos50.com.ar/dns/records

Como contrate un dominio por fuera de cloudflare me pedí identificarme como que mi dominio es realmente mio al parecer. Esto se llama **ICAN-Lookup**
#### [ICANN-LookUp](https://lookup.icann.org/es)
Cuando se configura esto genera un registro de [URL](https://rdap.nic.ar/domain/famosos50.com.ar) dentro de [Servidores Autorotativos](Servidores%20Autorotativos.md) . Esto parece tener que ver con algo llamado [RDAP](RDAP)

Cuando conectas un dominio que no es de testing, por lo menos con el hosting **bluehost** autoconecta al CDN de Cloudflare. Pero inicie una cuenta en cloudflare para poder administrar desde Cloudflare el trafico de la web. Desde ahi tenemos acceso a estadísticas basicas del sitios, Google Analitics y además podemos establecer algunas reglas de seguridad.

**UbicaciónConfiguración **[**Requeridanic.ar**](http://Requeridanic.ar)Nameservers: Solo los de Cloudflare (ej: ns1.cloudflare.com).**Cloudflare DNS**- Registro "A" para @ apuntando a IP de Bluehost + Proxy (nube naranja).

- Registro "CNAME" para www apuntando al dominio principal.**Bluehost**- Cloudflare integrado: **Desactivado**.
    
- Firewall: Permitir IPs de Cloudflare.
