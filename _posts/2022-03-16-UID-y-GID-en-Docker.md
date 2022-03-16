---
title: User ID y Group ID en Docker 
published: true
---

En este *blog* intentaremos entender y resolver un problema común que ocurre con Docker cuando en el contenedor usamos alguna herramienta que genere archivos durante el desarrollo. Algunos casos comunes de esto es:

- Cuando usamos alguna herramienta que descargue dependencias, como por ejemplo npm o composer.
- Cuando usamos alguna herramienta que realice alguna tarea de compilación, como compilar SCSS a CSS.


# Descripción del problema

Para entender el problema utilizaremos un contenedor que usa composer para gestionar sus dependencias:

```bash
docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    composer:2.2 composer require psr/log
```

Si listamos el directorio, veremos que el usuario propietario de las carpetas/archivos generados por composer es *root*:

```bash
$ ls -la
total 20K
drwxrwxr-x. 3 eva       eva       4.0K Aug  4 19:34 ./
drwxr-xr-x. 7 eva       eva       4.0K Aug  4 19:31 ../
drwxr-xr-x. 4 root      root      4.0K Aug  4 19:34 vendor/
-rw-r--r--. 1 root      root        53 Aug  4 19:34 composer.json
-rw-r--r--. 1 root      root      2.1K Aug  4 19:34 composer.lock
```

Por lo tanto, si intento modificarlo/eliminarlo obtendré un error de permisos:

```bash
$ echo '' > composer.json 
bash: composer.json: Permission denied
```

## ¿Por qué sucede esto?

Para entender esto es importante comprender como Linux gestiona la asignación de uid (identificador del usuario) y gid (identificador del grupo).

El kernel de Linux es el responsable de administrar el espacio uid y gid, y son las llamadas al sistema a nivel del kernel las que se utilizan para determinar si se deben otorgar los privilegios solicitados. Por ejemplo, cuando un proceso intenta escribir en un archivo, el kernel examina el uid y el gid que crearon el proceso para determinar si tiene suficientes privilegios para modificar el archivo. **Lo importante para otorgar o no privilegios es el uid, no el nombre de usuario**.

Cuando corremos contenedores docker, se sigue usando el mismo kernel que el del host. Es decir, todos los procesos que corren dentro de los contenedores comparten el mismo kernel, que es el mismo que el del host. Este kernel, como dijimos antes, gestiona todos los uid y gid.

Por lo tanto, no puede tener diferentes usuarios con el mismo uid dentro de diferentes contenedores. Entonces, ¿Por qué puedo definir diferentes nombres de usuarios (nombre de grupos) entre los contenedores que tienen el mismo uid (gid)? Esto se debe a que **el nombre de usuario (y los nombres de grupo) que aparecen en las herramientas comunes de Linux no forman parte del kernel, sino que son administrados por herramientas externas** (/etc/passwd, LDAP, Kerberos, etc.). Por lo tanto, es posible que vea diferentes nombres de usuario, ya que tanto el host como los contenedores tienen su propia lista separadas de usuarios y grupos en /etc/passwd y en /etc/group respectivamente, pero no puede tener diferentes privilegios para el mismo uid/gid.

Ahora bien, ¿por qué las carpetas/archivos generados por composer son propietarios de *root*?

El motivo por le cuál sucede esto es que Docker corre en Linux como *root*, si uno sigue las recomendaciones de la instalación oficial. Entonces, cuando creas un nuevo contenedor, este no es generado por el usuario actual, sino como *root* que es el *daemon* que está corriendo por debajo. Es importante considerar esto para construir un sistema seguro, **si no se proporciona ninguna otra opción, como pasar un uid diferente en el Dockerfile, los procesos en los contenedores se ejecutarán como *root***.

Podemos verificar esto de la siguiente manera: 

```bash
$ docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    composer:2.2 whoami
root
```

Como vemos, el resultado de ```whoami``` (que devuelve el usuario actual) en el contener es *root*. Si ejecutamos:

```bash
$ docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    composer:2.2 \
        bash -c "echo \$(id -u \${USER}):\$(id -g \${USER})"
0:0
```

Vemos que el id del usuario y grupo dentro del contener es 0.

    Es importante recordar que en Linux el usuario/grupo root tiene un id, por convención, que es 0.

## Solución 1

Para ejecutar el contenedor de Docker con el id de usuario y grupo actual del host, podemos pasarlo de la siguiente manera:

```bash
docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    composer:2.2 composer require psr/log
```

Si listado del directorio, veremos que el usuario propietario de las carpetas/archivos generados por composer es el usuario actual:

```bash
$ ls -la
total 20K
drwxrwxr-x. 3 eva eva 4.0K Aug  4 20:09 ./
drwxr-xr-x. 7 eva eva 4.0K Aug  4 19:31 ../
drwxr-xr-x. 4 eva eva 4.0K Aug  4 20:09 vendor/
-rw-r--r--. 1 eva eva   53 Aug  4 20:09 composer.json
-rw-r--r--. 1 eva eva 2.1K Aug  4 20:09 composer.lock
```

Dependiendo del proyecto que se este considerando, está solución puede tener un problema. Dentro del contenedor el usuario ya no es *root*. Esto es un problema si existe alguna aplicación dentro del contenedor que requiera raelizar ciertas cosas como *root* o un usuario predefinido. Por ejemplo, si se quiere ejecutar una aplicación web con PHP-FPM:


```bash
$ docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    -u $(id -u ${USER}):$(id -g ${USER}) \
    composer:2.2 touch /var/lib/php/sessions/foo
touch: cannot touch '/var/lib/php/sessions/foo': Permission denied
```

La carpeta /var/lib/php/sessions es propiedad de www-data:www-data, que muy probablemente no comparte los ids de sus usuarios locales: 

```bash
$ docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    composer:2.2 \
        bash -c "echo \$(id -u www-data):\$(id -g www-data)"
33:33
```

## Solución 2

Esta solución propone asignar identificaciones internos de usuarios/grupos con buenos valores conocidos. 

En el contenedor, www-data tiene el id 33:33. Lo que se puede hacer es cambiarlo para que www-data tenga el id de mi usuario/grupo del host.

Para esto necesitamos crear un Dockerfile que realicé está tarea:

```bash 
FROM composer:2.2

ARG USER_ID
ARG GROUP_ID

RUN if [ ${USER_ID:-0} -ne 0 ] && [ ${GROUP_ID:-0} -ne 0 ]; then \
    userdel -f www-data &&\
    if getent group www-data ; then groupdel www-data; fi &&\
    groupadd -g ${GROUP_ID} www-data &&\
    useradd -l -u ${USER_ID} -g www-data www-data &&\
    install -d -m 0755 -o www-data -g www-data /home/www-data &&\
    chown --changes --silent --no-dereference --recursive \
          --from=33:33 ${USER_ID}:${GROUP_ID} \
        /home/www-data \
        /.composer \
        /var/run/php-fpm \
        /var/lib/php/sessions \
;fi
        
USER www-data
```

Expliquemos este archivo Dockerfile. 

Básicamente lo que hacemos es pasar el id de nuestro usuario/grupo actual en el host como variable de entorno en el contenedor. 

Luego, en ```if [ ${USER_ID:-0} -ne 0 ] && [ ${GROUP_ID:-0} -ne 0 ]; then``` permite que las variables USER_ID y GROUP_ID sean opcionales. Si ambos no están definidos, omitimos por completo el proceso. Esto hace que el Dockerfile se puede usar para fines de desarrollo y producción.

En caso que estén definidas las variables, eliminamos el usuario y grupo www-data:

```bash 
    userdel -f www-data &&\
    if getent group www-data ; then groupdel www-data; fi &&\
```

Luego recreamos el usuario/grupo con los valores pdefinidos:

```bash 
    useradd -l -u ${USER_ID} -g www-data www-data &&\
```

Además generamos un directorio para el usuario www-data. Esto es útil si necesita que su contenedor realice acciones SSH utilizando las claves SSH de su host.

```bash 
    install -d -m 0755 -o www-data -g www-data /home/www-data &&\
```

En las siguiente línea, se le cambia de propietario a ciertas carpetas que pueden requirir actualización. De está manera evitamos problemas de permisos. Las carpetas a cambiar los permisos depende de cada conteneder. Deben ser aquellas que deben actualizarse. El Dockerfile de la imagen es la principal fuente que deberíamos mirar para averiguar que carpetas involucra agregar en este paso.

```bash 
    chown --changes --silent --no-dereference --recursive \
          --from=33:33 ${USER_ID}:${GROUP_ID} \
        /home/www-data \
        /.composer \
        /var/run/php-fpm \
        /var/lib/php/sessions \
```



Si ejecuta el contenedor ahora, se convertirá automáticamente en el usuario de www-data que ahora tiene el ID de usuario/grupo que el mismo usuario/grupo host. Todos los archivos generados por este contenedor serán propiedad del usuario de mi sistema host.


Para construir la imagen de arriba harías: 

```bash 
$ docker image build \
    --build-arg USER_ID=$(id -u ${USER}) \
    --build-arg GROUP_ID=$(id -g ${USER}) \
    -t php_test \
    .
```


Intente ejecutar Composer con la nueva imagen: 

```bash 
$ docker container run --rm \
    -v ${PWD}:/var/www \
    -w /var/www \
    php_test:latest composer require psr/log
```

y el resultado es el siguiente: 

```bash
$ ls -lan
total 24K
drwxrwxr-x. 3 1000 1000 4.0K Aug  4 22:50 ./
drwxr-xr-x. 7 1000 1000 4.0K Aug  4 19:31 ../
drwxr-xr-x. 4 1000 1000 4.0K Aug  4 22:50 vendor/
-rw-rw-r--. 1 1000 1000  545 Aug  4 22:48 Dockerfile
-rw-r--r--. 1 1000 1000   53 Aug  4 22:50 composer.json
-rw-r--r--. 1 1000 1000 2.1K Aug  4 22:50 composer.lock
```

### Usar Docker Compose

El docker-compose.yml equivalente para este ejemplo sería:

```bash
# docker-compose.yml
version: '3.2'
services:
  php:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        USER_ID: ${USER_ID:-0}
        GROUP_ID: ${GROUP_ID:-0}
    volumes:
      - ${HOME}/.composer:/.composer
      - ${PWD}:/var/www
    ports:
      - 9000:9000
```

El id del usuario/grupo deben definirse en un archivo .env, ya que Docker compose no tiene la capacidad de ejecutar comandos.

```
; .env
USER_ID=1000
GROUP_ID=1000
```

Para construir la imagen y correr el contenedor ejecutamos:

```
docker-compose up -d --build
```


# [](#header-1)Fuentes

- [Running Docker Containers as Current Host User](https://jtreminio.com/blog/running-docker-containers-as-current-host-user/#ok-so-what-actually-works/)
- [Esto no me lo imaginaba - UID y GIDs en Docker](https://www.youtube.com/watch?v=0xUwaz0MD_E)
- [Understanding how uid and gid work in Docker containers](https://medium.com/@mccode/understanding-how-uid-and-gid-work-in-docker-containers-c37a01d01cf)

