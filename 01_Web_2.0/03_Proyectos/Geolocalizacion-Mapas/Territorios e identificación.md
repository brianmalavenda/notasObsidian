tipo:: #web/proyectos
Tags: #geolocalizacion #geojson 
Links: [gobiernoabierto](https://gobiernoabierto.cordoba.gob.ar/data/datos-abiertos/categoria/geografia-y-mapas/barrios-de-la-ciudad/118) , [comunidadinteligente](https://comunidadinteligente.cordoba.gob.ar/catastro/)

Barrio con manzanas numeradas y cada una tiene un detalle a ser consultado
![[mapa_geolocalizacion_01.png]]

Cada manzana tiene un plano de manzana
![[mapa_geolocalizacion_02.png]]


### Formato GeoJson

Ejemplo de uso en proyecto: https://github.com/OpenDataCordoba/barrios


### Morón

```json
		{
            "centroide": {
                "lat": -34.6493861631091,
                "lon": -58.6198317111492
            },
            "id": "060568",
            "nombre": "Morón",
            "provincia": {
                "id": "06",
                "nombre": "Buenos Aires"
            }
        },
```
### Poligono san jose

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "coordinates": [
          [
            [
              -58.62638641007153,
              -34.66474688534708
            ],
            [
              -58.62781544875011,
              -34.66709760391499
            ],
            [
              -58.63079601513607,
              -34.67384715364092
            ],
            [
              -58.61936370570986,
              -34.68334931870818
            ],
            [
              -58.61181021555373,
              -34.67643265634679
            ],
            [
              -58.62638641007153,
              -34.66474688534708
            ]
          ]
        ],
        "type": "Polygon"
      }
    }
  ]
}
```
