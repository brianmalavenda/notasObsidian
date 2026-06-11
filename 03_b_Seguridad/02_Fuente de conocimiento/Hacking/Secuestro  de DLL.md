tipo::
tags:: #web/seguridad
fecha:: 2025-10-09
links::
links_externos::

Concepto: https://attack.mitre.org/techniques/T1574/001/
https://securelist.lat/detecting-dll-hijacking-with-machine-learning-in-kaspersky-siem/100398/
Detectar secuestro de DLL: https://securelist.lat/building-ml-model-to-detect-dll-hijacking/100396/

Los adversarios pueden abusar de los archivos de biblioteca de enlaces dinámicos (DLL) para lograr persistencia, escalar privilegios y evadir defensas. Las DLL son bibliotecas que contienen código y datos que pueden ser utilizados simultáneamente por múltiples programas. Si bien las DLL no son maliciosas por naturaleza, se puede abusar de ellas a través de mecanismos como la carga lateral, el secuestro del orden de búsqueda y el secuestro fantasma de DLL

Carga lateral de DLL
Los adversarios pueden ejecutar sus propias cargas útiles maliciosas mediante DLL de carga lateral. La carga lateral implica secuestrar qué DLL carga un programa plantando y luego invocando una aplicación legítima que ejecuta su(s) carga útil(s).

La carga lateral posiciona tanto la aplicación víctima como la(s) carga útil(es) maliciosa una al lado de la otra. Es probable que los adversarios utilicen la carga lateral como un medio para enmascarar las acciones que realizan bajo un sistema o proceso de software legítimo, confiable y potencialmente elevado. Es posible que los ejecutables benignos utilizados para cargar cargas útiles lateralmente no se marquen durante la entrega y/o ejecución. Las cargas útiles adversas también pueden cifrarse/empaquetarse u ofuscarse de otro modo hasta que se carguen en la memoria del proceso confiable.

Los adversarios también pueden cargar otros paquetes, como BPL (Borland Package Library).[2]

Secuestro de órdenes de búsqueda de DLL
Los adversarios pueden ejecutar sus propias cargas maliciosas secuestrando el orden de búsqueda que utiliza Windows para cargar las DLL. Este orden de búsqueda es una secuencia de ubicaciones de búsqueda especiales y estándar que un programa verifica al cargar una DLL. Un adversario puede colocar una DLL de troyano en un directorio que será priorizado por el orden de búsqueda de la DLL sobre la ubicación de una biblioteca legítima. Esto hará que Windows cargue la DLL maliciosa cuando el programa víctima la solicite.[1]

Redirección de DLL
Los adversarios pueden modificar directamente el orden de búsqueda mediante la redirección de DLL, lo que después de habilitarse (en el Registro o mediante la creación de un archivo de redirección) puede provocar que un programa cargue una DLL desde una ubicación diferente.[3][4]

Secuestro de DLL fantasma
Los adversarios pueden aprovechar el secuestro fantasma de DLL apuntando a referencias a archivos DLL inexistentes. Es posible que puedan cargar su propia DLL maliciosa colocándola con el nombre correcto en la ubicación del módulo faltante.[5][6]

Sustitución de DLL
Los adversarios pueden apuntar a archivos DLL válidos existentes y sustituirlos por sus propias DLL maliciosas, colocándolas con el mismo nombre y en la misma ubicación que el archivo DLL válido.[7]

Los programas que son víctimas del secuestro de DLL pueden parecer comportarse normalmente porque las DLL maliciosas pueden configurarse para cargar también las DLL legítimas que debían reemplazar, evadiendo las defensas.

El secuestro remoto de DLL puede ocurrir cuando un programa establece su directorio actual en una ubicación remota, como un recurso compartido web, antes de cargar una DLL.[8][9]

Si una DLL válida está configurada para ejecutarse en un nivel de privilegio superior, entonces la DLL controlada por el adversario que se carga también se ejecutará en el nivel superior. En este caso, la técnica podría utilizarse para la escalada de privilegios.

---
#### Referencias

