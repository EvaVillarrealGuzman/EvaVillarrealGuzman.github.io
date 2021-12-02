---
title: Limitación de recursos en Docker
published: true
---

De forma predeterminada, no hay límite en la cantidad de memoria o CPU que puede usar un contenedor. Puede usar tanto como lo permita el programador del *kernel* del *host*. Es importante que un contenedor en ejecución no consuma demasiada memoria o CPU en la computadora *host*. Cuando el *kernel* detecta que queda muy poca memoria libre, se activa una Excepción por falta de memoria y comienza a matar procesos, y esto puede llevar al cierre completo del sistema en algunos casos.

Para evitar estas circunstancias, siempre debe ejecutar pruebas en su aplicación y determinar la cantidad de recursos necesarios, y luego limitar los contenedores para usar una cantidad razonable de recursos.

Puede ver las estadísticas de sus contenedores docker sobre la marcha a través del comando ```stats``` de docker:

```bash
sudo docker stats [docker_image]
```

# [](#header-1)Habilitar limitación de recursos en el sistema

Antes de limitar los recursos de un *container* es importante verificar que el *kernel* permita esto. Para esto se debe ejecutar el siguiente comando:

```bash
sudo docker info
```

Si recibe la siguiente advertencia al final de la salida, debe realizar algunos cambios en los archivos del sistema.

```
WARNING: Noswaplimitsupport
```

Para habilitar la funcionalidad en el *kernel* debe editar el archivo que se encuentra en ```/etc/default/grub```, y luego agregar la siguiente línea:

```
GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
```

Guarde los cambios y actualice la configuración del GRUB ejecutando el comando:

```bash
sudo update-grub
```

Para que los cambios tengan efecto debe reiniciar el sistema:

```bash
sudo shutdown -r
```

Una vez reiniciado el sistema asegúrese de que los cambios se realizaron:

```bash
sudo docker info
```

No debería ver la advertencia ```WARNING: Noswaplimitsupport```.

# [](#header-1)Limitar el acceso a la memoria de un *container*

Existen diferentes limitaciones de la RAM que se pueden configurar en Docker. Algunas son:

## [](#header-2)Configurar la cantidad máxima de memoría que un contenedor puede usar

Para esto use la opción ```--memory``` del comando ```docker run```.  Dentro del comando se debe especificar cuanta memoria le quieres dedicar a un contenedor específico.

La sintaxis es la siguiente:

```bash
sudo docker run -it --memory="[memory_limit]" [docker_image]
```

```memory_limit``` debe ser un entero positivo seguido del sufijo b, k, m o g (que hacen referencia a bytes, kilobytes, megabytes o gigabytes respectivamente). 

Por ejemplo, para correr una instancia de un contenedor ubuntu y configurar un límite de 1GB, el comando sería el siguiente:

```bash
sudo docker run -it --memory="1g" ubuntu
```

## [](#header-2)Definir la cantidad de memoria que un contenedor puede intercambiar en el disco

El uso de la opción ```swamp``` permite almacenar datos incluso después de que se haya agotado toda la RAM asignada al contenedor. Lo hace ignorando la limitación de memoria y escribiendo directamente en el disco. Aunque esta es una característica útil, no es una práctica recomendada ya que perjudica la *performance*.

El ```swamp`` incluye la cantidad total de memoria no intercambiable (*non-swap memory*) más la cantidad de memoria intercambiable (*swap memory*) reservada como respaldo.

Por ejemplo, si se configuró ```--memory``` a 1 GB, la cantidad de memoria *swap* necesita ser mayor que eso. Para correr un contenedor con un adicional de 1 GB de memoria *swap*, se debe configurar el *swap* a 2 GB.

La sintaxis es:

```bash
sudo docker run -it --memory="[memory_limit]" --memory-swap="[memory_limit]" [docker_image]
```

Por ejemplo, para ejecutar un contenedor desde la imagen de Ubuntu, asignando 1 GB de RAM para que el contenedor lo use y reservando 1 GB de RAM para la memoria *swap*: 

```bash
sudo docker run -it --memory="1g" --memory-swap="2g" ubuntu
```

En caso de no querer usar memoria *swap*, dé a ```--memory``` y ```--memory-swap``` los mismos valores.

## [](#header-2)Configurar un límite flexible para la cantidad de memoria asignada a un contenedor 

Limitar el uso de memoria de un contenedor con ```--memory``` es establecer un límite estricto que no se puede superar. Alternativamente, puede establecer un límite flexible (```--memory-reserve```) que advierte cuando el contenedor llega al final de su memoria asignada, pero no detiene ninguno de sus servicios. 

Si no se establecen las limitaciones de ```--memory```, configurar el límite flexible con ```--memory-reserve``` no limita por completo el espacio del contenedor. Si tiene ambas funciones habilitadas, el límite flexible siempre es menor que la capacidad máxima de espacio. 

Como ejemplo, para que un contenedor de Ubuntu tenga la reserva de memoria de 750 MB y la capacidad máxima de RAM de 1 BG, el comando es:

```bash
sudo docker run -it --memory="1g" --memory-reservation="750m" ubuntu
```

# [](#header-1)Limitar el uso de CPU de un contenedor

Al igual que el uso de RAM, los contenedores Docker no tienen limitaciones predeterminadas para la CPU del *host*. Dar a los contenedores un uso ilimitado de la CPU puede generar problemas. 

Hay varias formas de definir la cantidad de recursos de CPU de la máquina *host* que desea asignar a los contenedores. 

Por ejemplo, si tiene un *host* con 2 CPU y desea otorgar acceso a un contenedor a una de ellas, use la opción ```--cpus = "1.0"```. El comando para ejecutar un contenedor de Ubuntu con acceso a 1 CPU sería: 

```bash
sudo docker run -it --cpus="1.0" ubuntu
```

Además, es probable que la máquina *host* tenga más de una CPU. Si ese es el caso, puede seleccionar la CPU, o las CPU, que deben ser asignadas por el contenedor. Esto le permite separar varios contenedores en diferentes CPU o núcleos. ```--cpuset-cpus``` acepta valores en dos formatos diferentes. Puede especificar las CPU deseadas mediante:

- una lista separada por comas. Como por ejemplo 0,2,4
- un rango. Como por ejemplo 1-3

Las CPUs se direccionan mediante un índice basado en cero. Esto significa que 0,2,4 le dice a Docker que asigne potencia de cálculo de la primera, tercera y quinta CPU. 

```bash
# Corre un contenedor y le asigna solo la segunda CPU
docker run -d --rm --cpuset-cpus 1 ubuntu -c 8 -t 20s
```

Debido a que el conjunto de CPU se selecciona explícitamente, el contenedor está limitado a la capacidad del conjunto. 

También puede usar la opción ```--cpu-shares``` para darle al contenedor una mayor o menor proporción de ciclos de CPU. De forma predeterminada, está configurado en 1024. Utilice números más bajos para definir una prioridad más baja; en cambio, los valores superiores a 1024 dan como resultado una prioridad más alta. 

Para ejecutar un contenedor con menos recursos compartidos de CPU, ejecute: 

```bash
sudo docker run -it --cpus-shares="700" ubuntu
```

# [](#header-1)Fuentes

- [How to Set Docker Memory and CPU Usage Limit](https://phoenixnap.com/kb/docker-memory-and-cpu-limit)
- [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/#configure-the-default-cfs-scheduler/)
- [CPU Shares for Docker containers](https://www.batey.info/cgroup-cpu-shares-for-docker.html)
- [Docker Container CPU Limits Explained](https://www.thorsten-hans.com/docker-container-cpu-limits-explained)