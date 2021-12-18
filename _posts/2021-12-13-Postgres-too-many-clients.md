---
title: PostgreSQL sorry, too many clients already
published: true
---

El parámetro ```max_connections``` determina el número máximo de conexiones simultáneas en el servidor de base de datos. Por defecto PostgreSQL establece el límite máximo de conexiones en 100. 

Si el límite establecido en la base de datos se supera, PostgreSQL lanzará la siguiente excepción:

```
org.postgresql.util.PSQLException: FATAL: sorry, too many clients already.
```

Ante este error podemos encontrar las siguientes soluciones:

1. Incrementar el parámetro ```max_connections```. Esto es sencillo de realizar pero probablemente solucione el problema temporalmente.
2. Una posible causa de este error es que las aplicaciones que se conectan a la base de datos no estén implementado correctamente el cierre de la conexiones. Generando que se sigan abriendo conexiones sin cerrar otras que ya no se usan. Por lo tanto debería chequear el código donde se abre una conexión y verificar que la misma se cierre correctamente. Para ayudar a encontrar la aplicación que hace un mal uso de la conexión, puede dar diferentes nombres de usuario / contraseñas a las aplicaciones que podrían no liberar las conexiones, y luego buscar en ```pg_stat_activity``` para averiguar cuál no se está cerrando las conexiones. 
3. Usar un *pool* de conexiones en las aplicaciones. Un *pool* de conexiones mantiene un número de conexiones a la base datos abiertas. De esta manera en lugar de abrir tu mismo una nueva conexión simplemente solicitas alguna de las disponibles, mejorando de esta forma la *performance* de tu aplicación, ya que abrir y cerrar conexiones de base de datos suponen un coste de procesador. Para más detalle ver [aquí](/Connections-pool).

Particularmente nos vamos a centrar sobre el punto 1, es decir, la configuración del parámetro ```max_connections```.

Sobre este parámetro debe tener en cuenta lo siguiente:

> Cada conexión PostgreSQL consume memoria RAM para administrar la conexión o el cliente que la usa. Cuantas más conexiones tenga, más memoria RAM se utilizará para este fin, en lugar de utilizarse para ejecutar la base de datos.

Por lo general, una aplicación bien codificada no necesita una gran cantidad de conexiones. Si tiene una aplicación que necesita una gran cantidad de conexiones, considere usar una herramienta como ```pg_bouncer``` que puede agrupar las conexiones por usted. Como cada conexión consume RAM, debería intentar minimizar su uso y por lo tanto es importante no abusar. 

## [](#header-2)Comandos útiles para modificar el parámetro max_connections


### [](#header-3)Chequeé el valor del parámetro actual

```sql
SHOW max_connections;
```

### [](#header-3)Ver con detalle las conexiones actualmente usadas

Este comando permite ver quién/qué/cuándo/dónde se mantiene abiertas las conexiones: 

```sql
SELECT * FROM pg_stat_activity;
```

### [](#header-3)Liste la cantidad de conexiones actualmente usadas


```sql
SELECT count(*) FROM pg_stat_activity;
```

### [](#header-3)Editar el parámetro max_connections

Sobre esto, es importante aclarar que incrementar solamente ```max_connections``` es una mala idea. También debería incrementar ```shared_buffers``` y ```kernel.shmmax```.

El parámetro ```shared_buffers``` determina cuanta memoria usará PostgreSQL para almacenar datos en caché. Las conexiones utilizan la memoria en los *shared buffers*.
Tenga en cuenta las siguientes consideraciones:

- Si tiene un servidor con 1 GB o más de RAM, un valor inicial razonable para ```shared_buffers``` es 25% de la memoria de su servidor. 
- Es poco probable que el uso de más del 40% de RAM funcione mejor que una cantidad menor (como el 25%) 
- Si su servidor o la compilación de PostgreSQL es de 32 bits, puede que no sea práctico establecer ```shared_buffers``` por encima de 2 ~ 2.5GB. 

Aclarado esto, continuaremos con los pasos para modificar estos parámetros.

Busque el archivo de configuración de PostgreSQL

```sql
SHOW config_file;
```

Edite el archivo de configuración y actualice el parámetro ```max_connections``` y ```shared_buffers```. Supongamos que tenemos un server con 8GB de memoria, y en el mismo archivo ```postgresql.conf```, actualice el valor del parámetro ```shared_buffers``` a 2GB (25%). 

```
max_connections = 100
shared_buffers = 2GB
```

Para que los cambios tengan efecto reinicie el servicio de PostgreSQL:

```bash
systemctl restart postgresql
```

Debería aumentar el tamaño del segmento máximo del kernel para que sea un poco más grande que ```shared_buffers```. En el archivo ```/etc/sysctl.conf```, configure el parámetro como se muestra a continuación. Entrará en vigor cuando se reinicie postgreSQL (la siguiente línea hace que el kernel tenga un máximo de 2.3GB) 

```
kernel.shmmax=2469606195
```

En los sistemas modernos, ```kernel.shmmax``` ya es muy alto y no debe cambiarse. Para ver cual está configurado puede ejecutar el siguiente comando:

```bash
cat /proc/sys/kernel/shmmax
```

> **Nota:** Puede usar la página [PGTune](https://pgtune.leopard.in.ua/) para obtener los parámetros óptimos para su sistema.

# [](#header-1)Fuentes

- [org.postgresql.util.PSQLException: FATAL: sorry, too many clients already](https://stackoverflow.com/questions/2757549/org-postgresql-util-psqlexception-fatal-sorry-too-many-clients-already)
- [Checking and Increasing the Connection Limit in PostgreSQL](https://dadruid5.com/2017/07/20/checking-and-increasing-the-max-connections-in-postgresql/)
- [How to resolve PostgreSQL FATAL: sorry, too many clients already](https://devcoops.com/resolve-sorry-too-many-clients-postgres-issue/)
- [How to increase the max connections in postgres?](https://stackoverflow.com/questions/30778015/how-to-increase-the-max-connections-in-postgres)