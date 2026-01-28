
tipo:: #web/proyectos
tags::
fecha:: 2025-09-02
links::
links_externos::

## Detalle

Registrar la lectura de diarios por compañero y balance mensual de cada uno. Puede extenderse al manejo de “privilegios” para mostrar algunas paginas para quienes son administradores y otros solo cargan la lectura.

## Aportes técnicos

Roles y privilegios, una web que tenga la posibilidad de realizar esas acciones, integrarse back y front.

## Primera iteración de la aplicación

Creamos una aplicacion con autenticacion a través de JWT. Usamos para el front NextJS y para el back NestJs, para la base de datos usamos Prisma, creamos una imagen de docker y un contenedor el cual usamos para deployar en Railway 

![[ejes_lectura_control_app_02.png]]

El plan de la plataforma de Railway que es un Server As a Service, un servidor como servicio o un Saas, nos permitió desplegar la app allí y tener visibilidad de traceo de usuarios que se logean en el servidor, como los errores que se van produciendo y demas. A esto se le suma la cantidad de bytes transferidos desde el servidor al cliente, solicitudes http filtradas por estado etc. Lo que nos permitió esto es ver cuanto nos cuesta mensualmente mantener una aplicacion de este tipo en un servidor como este.

Con esta app, a través del uso de 8 personas que interactuaron a modo de prueba calculamos que mensualmente nos cuesta hasta 5 US$ tener activa una web como esta. Obvio que esto depende de la cantidad de tráfico ya que se paga por uso, no es que se alquilar un espacio físico de memoria y disco sino que hay un tope de recursos por plan y un piso desde el cual se contrata determinados equipos. Esto se traduce mensualmente en 60 dólares, a precio de hoy 02/09/2025 casi $ 100.000.  ^app-code-presupuesto

![[ejes_lectura_app_01.png]]

## Mantenimiento

Despues de un mes de haber desplegado la aplicacion, tenemos un error en el versionado de la base de datos de Prisma que usamos en comparacion con la que tiene el servicio Railway.

![[error_railway_actualizacion_paquetes.png]]



---
#### Referencias

