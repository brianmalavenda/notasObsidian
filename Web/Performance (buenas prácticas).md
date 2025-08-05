Links: [[Herramientas Performance Web]]
Tags: #performance
# Larges contenful paint

Esto indica aproximadamente cuándo el contenido principal de la página es visible para los usuarios. Si el LCP es una imagen, el tiempo se puede dividir en cuatro subpartes. Saber qué subpartes tardan más puede ayudarle [a optimizar su LCP](https://web.dev/optimize-lcp/)

| Subparte LCP                       | Descripción                                                                                                                                               |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tiempo hasta el primer byte (TTFB) | El tiempo transcurrido desde que el usuario inicia la carga de la página hasta que el navegador recibe el primer byte de la respuesta del documento HTML. |
| Retraso de carga                   | La diferencia entre TTFB y cuando el navegador comienza a cargar el recurso LCP.                                                                          |
| Tiempo de carga                    | El tiempo que tarda en cargarse el recurso LCP en sí.                                                                                                     |
| Retraso de renderizado             | El delta entre cuando el recurso LCP termina de cargarse hasta que el elemento LCP se procesa por completo.                                               |
