tipo::
tags::
fecha:: 2025-09-19
links::
links_externos::

Desde este humilde lugar, Pearce opera [Searcha Page](https://searcha.page/) y su variante centrada en la privacidad, Seek Ninja. De esta manera, cuando se realiza una búsqueda en estas páginas, la consulta viaja directamente a un servidor que funciona entre ciclos de lavado. Y lo más increíble es que, salvo por una ligera latencia que Pearce ha ido mejorando, los resultados son de una calidad asombrosa.

"Ahora mismo, en el cuarto de la colada, tengo más capacidad de almacenamiento que la que tenía Google en el año 2000", afirma Pearce. Y esa es la clave de todo: la democratización de una tecnología que parecía inalcanzable.

**El hardware.** Pearce ha aprovechado un fenómeno que podríamos llamar "arbitraje de actualizaciones". Las grandes empresas renuevan sus servidores cada tres o cuatro años, desechando equipos increíblemente potentes que acaban en el mercado de segunda mano a precios de risa. Y ahí es donde Pearce trató de aprovechar al máximo. 

El cerebro de su sistema es una CPU AMD EPYC 7532 de 32 núcleos. En su lanzamiento en 2020, este procesador costaba más de 3.000 dólares. Pearce lo consiguió en eBay por menos de 200 dólares. El sistema completo, que incluye medio terabyte de memoria RAM, le costó unos 5.000 dólares, una fracción mínima de su valor original. Es un ejemplo perfecto de auto-alojamiento (_self-hosting_) llevado al extremo.

Es por ello que mientras muchos usuarios [critican los resúmenes generados por IA de Google](https://www.genbeta.com/buscadores/google-se-carga-concepto-clasico-buscador-para-dar-paso-a-busquedas-mediante-ia-no-tardaran-llegar-a-europa) o el chatbot de Bing, Pearce utiliza los modelos de lenguaje grandes (LLM) de una manera diferente. No los usa para darte una respuesta directa, sino como una herramienta de desarrollo para acelerar la creación del motor de búsqueda.

"Lo que estoy haciendo es en realidad una búsqueda muy tradicional, lo que Google hacía hace 20 años", explica Pearce. "La única diferencia es que uso la IA para la expansión de palabras clave y para ayudar con la comprensión del contexto, que es la parte difícil".

**Política de aprovechamiento.** De esta manera, en lugar de construir desde cero complejos algoritmos de procesamiento de lenguaje natural, Pearce aprovecha modelos como Llama 3 (a través del servicio de bajo coste **SambaNova**) para refinar las búsquedas. Esto le permite construir un sistema complejo de forma interactiva, escribiendo código tradicional para reemplazar gradualmente las funciones que inicialmente delega en la IA. Su código base ya tiene unas 150.000 líneas, pero ya apunta apunta a muchas más.

**No está solo.** El caso de Pearce no es aislado. Es la punta de lanza de una nueva tendencia de desarrolladores independientes que se atreven a reinventar la búsqueda. Otro ejemplo es Wilson Lin, quien, a diferencia de Pearce, optó por un enfoque basado en la nube. Lin ha creado su propio motor de búsqueda utilizando bases de datos vectoriales, donde los resultados se agrupan por similitud semántica en lugar de por coincidencia de palabras clave que es a lo que estamos acostumbrados. 

Ambos proyectos, aunque con filosofías distintas (uno auto-alojado y tradicional, el otro en la nube y vectorial), dependen de dos pilares fundamentales:

1. Modelos de IA accesibles: Los LLMs han reducido drásticamente la barrera de entrada para el desarrollo de sistemas complejos de comprensión del lenguaje.
2. Datos abiertos: Ambos se apoyan en Common Crawl, un repositorio público y masivo de datos web que sirve como base para entrenar IAs y construir índices de búsqueda sin tener que rastrear toda la web desde cero.

**Buscadores alternativos.** Estos proyectos se unen a una creciente familia de buscadores alternativos como [DuckDuckGo](https://www.genbeta.com/buscadores/duckduckgo-gran-rival-google-centrado-privacidad-supera-primera-vez-100-millones-busquedas-diarias), Brave Search o el más nicho Marginalia, que se centra en descubrir sitios web pequeños y no comerciales. Lo que demuestran Pearce y Lin es que la tecnología para construir un buscador ya no es el principal obstáculo. "La única barrera para competir realmente con Google", afirma Lin, "no es tanto la tecnología, sino principalmente las fuerzas del mercado".