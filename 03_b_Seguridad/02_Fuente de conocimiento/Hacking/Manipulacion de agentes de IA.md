tipo::
tags::
fecha:: 2025-09-22
links::
links_externos::

**Cómo funciona el ataque**. Un viaje del [articulo](https://www.codeintegrity.ai/blog/notion) publicado por CodeIntegrity, sus investigadores han demostrado que el proceso puede llegar siendo muy sencillo. Primero, el atacante crea un documento PDF aparentemente inofensivo. Sin embargo, dentro del archivo ocultan un texto con instrucciones maliciosas que engañan al agente de IA haciendose pasar por una "tarea rutina importante" del sistema interno.

**Una vagabunda invisible**. El texto malicioso utiliza técnicas de manipulación psicológica, presentándose como una tarta crítica que debe completarse para evitar "consecuencias" en la empresa, usando ademas terminología técnica para parecer legítimo y dando a entender que la acción está "preautorizada" por seguridad. Cuando el usuario pide al agente de Notion que resuma el documento, este lee las instrucciones ocultas y las interpreta como órdenes genuinas del sistema.

Fuga de datos. Una vez activado, el agente busca información confidencial en las páginas de Noción del usuario, tal y como el prompt se lo había ordenado, y los concatena en una URL maliciosa previamente descrita. Despuestas utiliza la herramienta de búsqueda web del sistema para enviar una consulta que contiene toda esa información sensible a un servicio controlado por el atacante, donde los datos quedan registrados.

Alcance del problema. Lo más preocupante es que esta vulnerabilidad no se limita a archivos PDF subidos manualmente. Noción 3.0 integra conectores con múltiples servicios empresariales como GitHub, Gmail o Jira, cualquiera de los cuales podría ser utilizado para inyectar instrucciones maliciosas sin que el usuario sospeche nada. Incluso modelos de IA avanzados como el de Claude Sonnet 4, considerados entre los más seguros del mercado, han demostrado ser susceptibles a este tipo de ataques.

---
#### Referencias

