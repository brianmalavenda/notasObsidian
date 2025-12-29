tipo::
tags::
fecha:: 2025-11-14
links::
links_externos::


Evitar apps que hagan pooling

La app **"text"** está haciendo:

- `POST /session/sync` cada minuto
- `POST /session/push` cada minuto
- `POST /session/close` y `PUT /session/create` constantemente

```bash
docker exec -u 33 nextcloud-app php occ app:disable text
docker exec -u 33 nextcloud-app php occ app:disable dashboard
```


---
#### Referencias

