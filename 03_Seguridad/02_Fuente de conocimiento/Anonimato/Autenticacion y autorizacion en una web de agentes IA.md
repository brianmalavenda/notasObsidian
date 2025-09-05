tipo:: [[Seguridad Web]]
tags:: #cloudflare #autenticacion #autorizacion 
fecha:: 2025-08-29
links:: 
links_externos:: [positiveblue](https://positiveblue.substack.com/p/the-web-does-not-need-gatekeepers)


El nuevo discurso de Cloudflare sobre los " **agentes firmados** " suena a seguridad, pero es una farsa. Han creado una lista de permitidos para la web abierta y han pedido a los desarrolladores que soliciten permiso. Internet no funciona así. Un [formulario de solicitud](https://dash.cloudflare.com/?to=/:account/configurations/verified-bots) no es un estándar.

Sí, la identidad de los agentes es un problema real. Pero Cloudflare lo está resolviendo como un puesto de control fronterizo. Si no te apuntas a su lista, te tratarán como a un intruso. Eso es aprobación de proveedores, no un protocolo de internet. ¿Una lista de permitidos gestionada por una sola empresa?

La autenticación para ese mundo no se trata de "pedirle a Cloudflare un pase libre". Se trata de cadenas verificables de delegación y pruebas a nivel de solicitud: abiertas, portátiles e independientes de cualquier empresa.

**Los agentes de IA estarán dirigidos explícitamente por humanos; en otras, actuarán como subrutinas dentro de tareas más complejas** . 

>[!quote] PositiveBlue
Ningún guardián único decide quién es “válido”. Necesitamos protocolos, no guardianes.

Piensa en una cuenta bancaria: Podría decirle a mi agente _"paga la cena"._ Ese token debería permitir el pago. Pero cuando le pido _"muéstrame tres meses de gastos",_ el agente no debería poder mover dinero. Mismo agente, diferente tarea, diferente token. **Las credenciales deben seguir a las tareas, no a los agentes.**

Leer mas ...

---
#### Referencias
[Cloudflare es ahora la barrera de internet?](https://positiveblue.substack.com/p/the-web-does-not-need-gatekeepers)
