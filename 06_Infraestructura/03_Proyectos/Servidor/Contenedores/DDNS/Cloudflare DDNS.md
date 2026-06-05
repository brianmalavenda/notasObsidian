tipo::
tags::
fecha:: 2026-05-26
links:: [[Cloudflare]]
links_externos::


```yaml
version: '2'

services:
  ddns-sanmartinici:
    image: oznu/cloudflare-ddns:latest
    container_name: ddns-sanmartinici
    dns:
      - 10.0.17.12
    restart: always
    environment:
      - API_KEY=cfut_nTV0AH2yqE8LOugVN4vq9hyjbjU7MVsg2sbvkDam79c3189e
      - ZONE=sanmartinici.com.ar
      - SUBDOMAIN=andes,office
      - PROXIED=true

#  cloudflare-ddns-otraempresa:
#    image: oznu/cloudflare-ddns:latest
#    container_name: cloudflare-ddns-otraempresa
#    restart: always
#    environment:
#      - API_KEY=tu_token_de_api_de_cloudflare
#      - ZONE=otraempresa.com.ar
#      - SUBDOMAIN=nube,web
#      - PROXIED=true
```



---
#### Referencias

