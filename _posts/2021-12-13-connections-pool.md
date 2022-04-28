---
layout: post
title:  Connection Pooling
subtitle: In this article we’ll explain what's a connection pool and how this can help the scalability and performance of an application.
categories: postgres
tags: [db, postgres]
---

Database connections are fairly expensive operations. Such operation involved the following sequence of steps:

1- Opening a connection to the database using the database driver  
2- Opening a TCP socket  
3- Execute the SQL query over the socket  
4- Closing the connection  
5- Closing the socket  

In a production environment where we expect thousands of concurrent open and closed connections from clients, the steps explained above for each connection can cause performance to deteriorate, impacting a poor user experience, lost revenue, and even unscheduled downtime. Therefore, the database connections should be kept to a minimum in all possible use cases.

## ¿What's Connection Pooling?

Connection Pooling is a data access pattern, whose main purpose is to reduce the overhead involved in performing database connections and read/write database operations. It's a database connection cache implementation which can be configured to suit specific requirements.

The connections pools reuse some existing connection. It prevents the overhead of creating a new connection to the database every time there is a request for a database connection with the same properties (i.e name, database, protocol version). Instead of opening and closing connections for every request, connection pool uses a cache that can be reused when future requests to the database are required. Because traffic is never constant, the connection pool can manage traffic peaks without causing outages.

## ¿How does Connection Pooling work?

 A pool contains two types of connections:

- **Active connection**: In use by the application.
- **Idle connection**:  Available for use by the application. 

Every time the app gets a connection, the pool creates a new connection only if needed, that is, every connection in the pool is being used (active). Instead, if the pool have connections that are not currently in use (idle), the app will use one of those. The pool keeps a number of connections ready to be used defined by minIdle and maxIdle parameters. When the application close the connection, the pool does not close the physical connection, but becomes idle to be reused. This favors the scalability and performance of an application.

## Ways to integrate a Connection Pooling

- **As external service or middleware**: The connection poolers such as pgbouncer and pgpool-II can be used to pool connections from clients to a  PostgreSQL database. The connection pooler is between the application and the database server.
- **Client side libraries**: There are libraries that extend the functionality of the database driver to include support for connection pooling.

The best way to implement connection pooling for applications is to make use of an external service or middleware, as it is easier to configure and manage. Also, external middleware like pgpool-II provides other features, like load balancing.

## Parameter settings

Different applications may have different requirements to database connections and you may want to modify the default behavior of connection pooling. Settings such as pool size and connection timeouts can improve the overall performance of the connection pool, as well as connection availability.

In many cases, the best way to tune a connection pool for a specific applications is to try different combinations of the property values until a optimal performance is achieved.

### Control pool size

The connection pool allows the number of connections to be dynamic as demand increases and decreases. This helps conserve system resources that are otherwise wasted maintaining unnecessary connections.

#### Set initial pool size

Initial pool size specifies the number of available connections that are created when connection pool is initially created o reset.

By default, PostgreSQL has the value zero, which means that there are no previously created connections.

If the initial pool size is greather than the maximum pool size, only the maximum pool size of connections are initialized. In the case that the initial pool size is less than the minimum pool size, only the initial pool size of connections are initialized and held until enough connections are created to meet the minimum pool size value.

#### Set minimum pool size

Minimum pool size specifies the minimum number of available and borrowed connections that a pool maintains. A connection pool always tries to return to the specified minimum size unless the minimum amount has not yet been reached. For example, if the minimum limit is set to ten and only two connections have been created and borrowed, then the number of connections held by the pool remains two.

By default, PostgreSQL has the value zero.

#### Set maximum pool size

Maximum pool size specifies the maximum number of connections that a pool maintains. If the maximum number of connections are borrowed, connections will not be available until a connection is returned to the pool.

This property ensures that the pool does not grow to the point of exhausting a system's resources, which ultimately affects an application's performance and availability.

A value of zero indicates that there are no connections managed by the pool. An attempt to get a connection result in this situation is an exception.

### Connection control

Connections that become stale can affect connection availability. It is necessary to make an effort to close them, because otherwise unused connections for long periods of time can be wasted.

It is good practice to close all connections that are no longer needed by an application. Closing connections help minimize the number of connections that become stale but continue to be borrowed.

#### Maximum connection reuse count

This property specifies the maximum number of times any connection can be reused after which the pool removes and closes a connection. The value must be greater than 0 for this feature to be enabled. For example, if the specified value is 100, then when a connection is reused or borrowed 100 times from the pool, it is closed and removed from the pool. Connections are closed gracefully after they are returned to the pool and the property value has been exceeded. 

Default is 0, which means this feature is not enabled.

#### Maximum connection reuse time

This property specifies the maximum time any connection can potentially be reused after which the pool removes and closes a connection. The value is specified in seconds and must be greater than 0. For example, if the specified value is 3600 seconds, then when a connection is in existence for more than 3600 seconds, the connection is closed and removed from the pool. Connections are closed gracefully after they are returned to the pool and the specified property value has been exceeded. 

Default is 0, which means this feature is not enabled.

#### Abandoned connection timeout

This timeout determines how long a borrowed connection can remain unused before it is considered as abandoned and reclaimed by the connection pool. 

Setting the value to 0 disables abandoned connect.

#### Connection wait timeout

This property specifies the amount of time to wait for a used connection to be released by a client. This only applies when the maximum number of connections has been borrowed from the connection pool. When a client tries to borrow a connection from the pool and all connections are in use, the connection pool waits for a connection to be released back to the pool.

#### Innactive connection timeout

This timeout determines how long an available connection remains in the connection pool before it is removed from the pool.

Setting the value to 0 disables inactive connection timeout processing.

## Sources

- [Manejo del pool de conexiones](http://www.juntadeandalucia.es/servicios/madeja/contenido/recurso/218)
- [Pool de Conexiones Java](https://es.stackoverflow.com/questions/46754/pool-de-conexiones-java)
- [¿cuál sería la diferencia entre usar un pool o usar una conexion tradicional a la base de datos?](https://es.stackoverflow.com/questions/359715/cu%C3%A1l-ser%C3%ADa-la-diferencia-entre-usar-un-pool-o-usar-una-conexion-tradicional-a-l)
- [A Simple Guide to Connection Pooling in Java](https://www.baeldung.com/java-connection-pooling)
- [Improve database performance with connection pooling](https://stackoverflow.blog/2020/10/14/improve-database-performance-with-connection-pooling/)
- [node.js + mysql connection pooling](https://stackoverflow.com/questions/18496540/node-js-mysql-connection-pooling/54224377#54224377)
- [Oracle Universal Connection Pool for JDBC Java API Reference
11g Release 2 (11.2)](https://docs.oracle.com/cd/E11882_01/java.112/e12826/oracle/ucp/jdbc/PoolDataSource.html)
