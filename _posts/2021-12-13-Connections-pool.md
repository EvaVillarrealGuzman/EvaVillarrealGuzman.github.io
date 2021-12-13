---
title: Connection Pooling
published: true
---

Conectarse a una base de datos es una operación costosa, ya que consta de los siguientes pasos:

1. Abrir una conexión a la base de datos utilizando el *driver* de la base de datos. 
1. Abrir un socket TCP
1. Ejecutar *query SQL* sobre el *socket*.
1. Cerrar la conexión.
1. Cerrar el *socket*. 

En un entorno de producción en el que esperamos miles de conexiones abiertas y cerradas simultáneas de los clientes, realizar los pasos anteriores para cada conexión puede hacer que la base de datos funcione mal. Si ese rendimiento de la base de datos se deteriora, puede provocar una mala experiencia del usuario, pérdidas de ingresos e incluso tiempo de inactividad no programado. Por lo tanto, las conexiones a la base de datos deben reducirse al mínimo en todos los casos de uso posibles.

## [](#header-2)¿Qué es el *Connection Pooling*?

*Connection Pooling* es un patrón de acceso a datos, cuyo objetivo principal es reducir la sobrecarga involucrada en la realización de conexiones de base de datos y operaciones de base de datos de lectura/escritura. Es una implementación de caché de conexión de base de datos, que se puede configurar para adaptarse a requisitos específicos.

Los *pools* de conexiones reutilizan algunas conexiones existentes. Esto evita la sobrecarga de crear una nueva conexión a la base de datos cada vez que se solicita una conexión con las mismas propiedades (es decir, nombre, base de datos, versión del protocolo). En lugar de abrir y cerrar conexiones para cada solicitud, el *pool* de conexiones utiliza un caché de conexiones de base de datos que se puede reutilizar cuando se requieran futuras solicitudes a la base de datos. Permite que su base de datos se amplíe de manera eficaz a medida que aumentan los datos almacenados allí y la cantidad de clientes que acceden a ella. El tráfico nunca es constante, por lo que el *pool* puede gestionar mejor los picos de tráfico sin provocar interrupciones.

## [](#header-2)¿Cómo funciona el *Connection Pooling*?

Un *pool* contiene dos tipos de conexiones:

- Conexión activa (*Active*): en uso por la aplicación.
- Conexión inactiva (*Idle*): disponible para que la utilice la aplicación. 

El *pool* lo que hace es mantener y gestionar un número de conexiones físicas, que se irán reutilizando automáticamente para aumentar la eficiencia. De esta forma cuando creamos una nueva conexión desde nuestra aplicación, lo que obtenemos es una conexión lógica gestionada por el *pool*, y al cerrarla lo que hacemos es devolverla a dicho *pool*. A su vez el *pool* es capaz de ofrecer múltiples conexiones lógicas utilizando un reducido número de conexiones reales. El manejo de un *pool* favorece la escalabilidad y el rendimiento de una aplicación.

Cada vez que obtienes una conexión, el *pool* crea una nueva conexión solamente si es necesario, si tiene conexiones que actualmente no están en uso (*idle*), te devuelve una de estos. Por eso el *pool* mantiene una cantidad de conexiones listo para ser usado (*minIdle*-*maxIdle*). Es importante cerrar la conexión cuando terminaste de usarla. Cuando en la aplicación se cierra una conexión, en realidad el *pool* no cierra la conexión física, sino que pasa a estar *idle* para ser reutilizado.

## [](#header-2)Formas de integrar un *Connection Pooling*

### [](#header-3)Como servicio externo o middleware como pgbouncer 

Los *Connection poolers* como pgbouncer y pgpool-II se pueden utilizar para agrupar conexiones de clientes a una base de datos PostgreSQL. El *connection pooler* se encuentra entre la aplicación y el servidor de la base de datos. 

### [](#header-3)Librerías del lado del cliente

Existen librerías que amplían la funcionalidad del *driver* de la base de datos para incluir la compatibilidad con la *connection pooling*.


Sin embargo, la mejor manera de implementar *connection pooling* para aplicaciones es hacer uso de un servicio externo o *middleware*, ya que es más fácil de configurar y administrar. Además, el *middleware* externo como pgpool2 proporciona otras características, como *load balancing*, además de *pooling connections*. 

## [](#header-2)Buenas prácticas

Diferentes aplicaciones pueden tener requisitos de conexión de bases de datos diferentes y puede que desee modificar el comportamiento predeterminado de la *connection pooling*. Configuraciones como el tamaño del *pool* y tiempos de espera de conexión pueden mejorar el desempeño general del *pool* de conexión, así como la disponibilidad de conexión. En muchos casos, la mejor forma de afinar un *pool* de conexiones para una aplicación específica es intentar diferentes combinaciones de las propiedades utilizando diferentes valores hasta conseguir un rendimiento óptimo.

### [](#header-3)Controlar el tamaño del Pool

Se permite que el número de conexiones en el *pool* sea dinámico para aumentar y disminuir a medida que aumenta la demanda y disminuye. Este comportamiento dinámico ayuda a conservar los recursos del sistema que en otro caso se pierden en el mantenimiento de las conexiones innecesarias.

#### [](#header-4)Ajuste el tamaño del grupo inicial

El tamaño del *pool* inicial de la propiedad especifica el número de conexiones disponibles que se crean cuando el *pool* de conexiones es inicialmente creada o se reinicializa. Esta propiedad se utiliza normalmente para reducir el tiempo de arranque necesario para el purgado del *pool* a su tamaño óptimo.

Un valor de cero indica que no hay conexiones creadas previamente. El valor predeterminado es cero.

Si el tamaño del grupo inicial es mayor que el máximo tamaño del *pool*, sólo el número máximo de conexiones son inicializadas. En el caso que tamaño del grupo inicial de la propiedad es menor que el mínimo tamaño del *pool*, sólo el número inicial de las conexiones son inicializadas y se mantienen hasta que se crean conexiones suficientes para cumplir con el mínimo valor de tamaño del *pool*.

#### [](#header-4)Ajuste el tamaño mínimo del *pool*

El tamaño mínimo del *pool* especifica la cantidad mínima de las conexiones disponibles y prestadas que un grupo mantiene. Un grupo de conexiones siempre trata de volver al tamaño mínimo especificado a menos que la cantidad mínima aún no se ha alcanzado. Por ejemplo, si el límite mínimo se establece en diez y sólo dos conexiones se han creado y prestado, entonces el número de conexiones que mantiene el *pool* permanece en dos.

Esta propiedad permite que el número de conexiones del pool disminuya a medida que disminuye la demanda. Al mismo tiempo, la propiedad se asegura de que los recursos del sistema no se desperdician en el mantenimiento de las conexiones que son innecesarias. El valor predeterminado es cero. 

#### [](#header-4)Establecer el tamaño máximo de Pool

El tamaño máximo especifica el número máximo de las conexiones que mantiene un *pool*. Si se toman prestadas el número máximo de conexiones, las conexiones no estarán disponibles hasta que una conexión se devuelve al *pool*.

Esta propiedad permite que el número de conexiones en el pool aumente a medida que aumenta la demanda. Al mismo tiempo, la propiedad asegura que el *pool* no crezca hasta el punto de agotar los recursos de un sistema, que en última instancia, afecta al rendimiento de una aplicación y disponibilidad.

Un valor de cero indica que no hay conexiones gestionada por el *pool*. Un intento de obtener un resultado de conexión en esta situación, es una excepción.

### [](#header-3)Control de conexiones

Las conexiones que quedan obsoletas pueden afectar a la disponibilidad de conexión. Es necesario hacer un esfuerzo por cerrarlas, porque de otro modo se pueden desperdiciar conexiones no utilizadas por largos períodos de tiempo.

Es una buena práctica cerrar todas las conexiones que ya no son necesarias para una aplicación. Las conexiones de cierre ayudan a minimizar el número de conexiones que quedan obsoletas pero continúan prestadas. Las principales características a configurar:

- Configuración de reutilización de la conexión: Se configuran las características para poder reutilizar una conexión. Cuando supera un numero de reutilizaciones, se elimina del *pool*.
- Tiempo máximo de reutilización: Permite controlar el tiempo necesario para que una conexión pueda volver a ser reutilizada. Mantiene como ocupadas, conexiones dentro del *pool* hasta que superan el tiempo estipulado.
- Tiempo de espera para considerar una conexión abandonada: El tiempo de espera que se permite a los abandonados para recuperar la conexión de nuevo en el grupo de conexión después de que una conexión no ha sido utilizado por un importe específico de tiempo.
- Ajuste del Tiempo de espera de conexión: El tiempo de espera que una solicitud de conexión específica espera para obtener una conexión, si ya no hay ninguna conexión en el *pool*. La solicitud recibe una excepción si se alcanza el valor de tiempo de espera. Esta característica mejora la usabilidad de tiempo de espera global de la aplicación, minimizando la cantidad de tiempo que una aplicación está bloqueada y ofrece la capacidad de implementar una recuperación.
- Ajuste del tiempo de espera de conexión inactiva: Especifica el tiempo que una conexión disponible puede permanecer inactiva antes de que se cierre y se retire del *pool*. Esta propiedad de tiempo de espera sólo es aplicable a las conexiones disponibles y no afecta a las conexiones que estén prestadas.

# [](#header-1)Fuentes

- [Manejo del pool de conexiones](http://www.juntadeandalucia.es/servicios/madeja/contenido/recurso/218)
- [Pool de Conexiones Java](https://es.stackoverflow.com/questions/46754/pool-de-conexiones-java)
- [¿cuál sería la diferencia entre usar un pool o usar una conexion tradicional a la base de datos?](https://es.stackoverflow.com/questions/359715/cu%C3%A1l-ser%C3%ADa-la-diferencia-entre-usar-un-pool-o-usar-una-conexion-tradicional-a-l)
- [A Simple Guide to Connection Pooling in Java](https://www.baeldung.com/java-connection-pooling)
- [Improve database performance with connection pooling](https://stackoverflow.blog/2020/10/14/improve-database-performance-with-connection-pooling/)
- [node.js + mysql connection pooling](https://stackoverflow.com/questions/18496540/node-js-mysql-connection-pooling/54224377#54224377)