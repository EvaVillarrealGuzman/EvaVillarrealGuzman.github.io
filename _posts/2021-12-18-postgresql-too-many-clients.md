---
layout: post
title:  Sorry, too many clients already - error PostgreSQL
subtitle: In this article we’ll see different strategies to solve this error, how to perform one of them and commands will be useful to analyze this type of error
categories: postgres
tags: [db, postgres]
---

The ```max_connections``` parameter determines the maximun number of simultaneous connections that the database server can handle. By default, PostgreSQL sets this parameter to 100. 

When this limit is exceeded, PostgreSQL will throw the next exception:

```
org.postgresql.util.PSQLException: FATAL: sorry, too many clients already.
```
## Solutions

Faced with this error, we can find the following solutions:

1. Increase the ```max_connections``` parameter. This is the most easiest one, but probably will work as a temporary solution.
2. Ensure that applications close the connection to the database. To help you find the applications that isn't closing the connection, you could give different usernames/passwords to each application that might not releasing the connections, and then, with ```pg_stat_activity``` you can find out the application that is not closing.
3. Use a connection pool in applications. A connection pool keeps a number of open connections to the database. In this way, the application, instead of opening a new connection, can use one of those available in the pool, improvement the performance of the application because open and close connections in database incur a processor cost. For more detail see [here](/Connections-pool).

Particularly in this post we are going to focus on the solution 1, that is, the configuration of ```max_connections``` parameter.

About this parameter, you have in mind the following:

> Each PostgreSQL connection consumes RAM for managing the connection or the client using it. The more connections you have, the more RAM you will be using that could instead be used to run the database.

Typically, a well-written application doesn't need a large number of connections. If you have an application that needs a large number of connections, then consider using a tool which can pool connections. As each connection consumes RAM, you should be looking to minimize their use.

## Useful command to see the connections in the database

### Check the current value of the parameter

```sql
SHOW max_connections;
```

### See in detail the currently used connections

```sql
SELECT * FROM pg_stat_activity;
```

This command allow you see who/what/where/when is holding open your connections.

### Get the number of connections currently used

```sql
SELECT count(*) FROM pg_stat_activity;
```

## Edit max_connections parameter

Just increasing ```max_connections``` is bad idea. You need to increase ```shared_buffers``` and ```kernel.shmmax``` as well. ```max_connections``` and ```shared_buffers``` are PostgreSQL parameters and ```kernel.shmmax``` is a kernel parameter.

The ```shared_buffers``` parameter determines how much memory is dedicated to PostgreSQL to use for caching data.

You can following the next rules to configure ```max_connections```, ```shared_buffers``` and ```kernel.shmmax``` parameters:

- If you have a server with 1 GB or more of RAM, a reasonable initial value for  ```shared_buffers``` is 25% of the memory of your server.
- It's unlikely you'll find using more than 40% of RAM to work better than a smaller amount (like 25%).
- Be aware that if your server or PostgreSQL build is 32-bit, it might not be practical to set ```shared_buffers``` above 2 ~ 2.5GB.

To edit these parameters, first find the location of the PostgreSQL config file:

```sql
SHOW config_file;
```

Open the PostgreSQL config file  and update the ```max_connections``` and ```shared_buffers``` parameters. For example, if you have a server with 8 GB of memory edit the value of ```shared_buffers``` parameter to 2 GB (25%) in ```postgresql.conf``` file:

```
max_connections = 100
shared_buffers = 2GB
```

Restart the PostgreSQL service:

```bash
systemctl restart postgresql
```

You should increase kernel max segment size to be slightly larger than the ```shared_buffers```. In the file ```/etc/sysctl.conf```, set the ```kernel.shmmax``` parameter as following:

```
kernel.shmmax=2469606195
```

It will take effect when PostgreSQL reboots. This line makes the kernel have a maximun of  2.3 GB.

On modern systems, ```kernel.shmmax``` it's already set high and should not be changed. To check the value that is setting in your system, you can run the following command:

```bash
cat /proc/sys/kernel/shmmax
```

> **Note:** You can use the page [PGTune](https://pgtune.leopard.in.ua/) to get the optimal parameters for your system.

## Sources

- [org.postgresql.util.PSQLException: FATAL: sorry, too many clients already](https://stackoverflow.com/questions/2757549/org-postgresql-util-psqlexception-fatal-sorry-too-many-clients-already)
- [Checking and Increasing the Connection Limit in PostgreSQL](https://dadruid5.com/2017/07/20/checking-and-increasing-the-max-connections-in-postgresql/)
- [How to resolve PostgreSQL FATAL: sorry, too many clients already](https://devcoops.com/resolve-sorry-too-many-clients-postgres-issue/)
- [How to increase the max connections in postgres?](https://stackoverflow.com/questions/30778015/how-to-increase-the-max-connections-in-postgres)
