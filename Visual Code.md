tipo:: #web/desarrollo 
tags::
fecha:: 2025-09-12
links:: [[Visual Code]]
links_externos::


### Code Speel

Hay una exension de visual code que busca palabras "mal escritas" y las subraya, ensuciando la visualizacion del código. 

![[error_spell_visual_code.png]]

Si por error se activa hay que ir a las preferencias de configuracion con Crtl + Shift + P y seleccionar la opción de Open Setting (JSON) y agregar la línea

```
	 "cSpell.enableFiletypes": ["!python"],
```

esto evitará que en siertos lenguajes esto no se active.

![[config_speel_visual_code.png]]


---
#### Referencias

