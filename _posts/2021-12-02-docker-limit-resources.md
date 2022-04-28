---
layout: post
title: How to set Docker Resources Limit
subtitle: In this article we’ll explain how to enable resources limitation in Docker, and how to set CPU and memory limits in containers.
categories: docker
tags: [docker]
---

By default, Docker don't limit the amount of memory and CPU that a container can use. The container can use as much of a given resources as the host's kernel scheduler allows. 

It's important to limit the resources that can consume a container. When the kernel detect that the computer have little memory, it's throws an ```Out of Memory Exception``` and starts killing process to free up memory, and this can bring the entire system down. To avoid that circunstancies, you must execute test in your application and calculate the amount of resources needed, and then limit the container to use a rational amount of resources.

You can view the statistics of your Docker containers with the  ```stats``` command:

```bash
sudo docker stats [docker_image]
```

## Configure system to enabled limiting resources

Before limiting the resources of a container it's important to check that the kernel of the host allow this. Run the following command:

```bash
sudo docker info
```

If you receive the following warning in the output:

```output
WARNING: Noswaplimitsupport
```

So, you must enable the functionality in the kernel. To achieve this, edit the file that is located in ```/etc/default/grub```, and then add the following line:

```
GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
```

Save the changes and update the GRUB configuration with the command:

```bash
sudo update-grub
```

Then, reboot your machine for the changes to take place:

```bash
sudo shutdown -r
```

Once the machine has restarted, make sure that the changes have been made:

```bash
sudo docker info
```

You shouldn't received the warning ```WARNING: Noswaplimitsupport``` this time.

## Limit Container Memory Access

There are different ways to limit RAM to containers:

### Configuring the maximum amount of memory a container can use

Add the  ```--memory``` option to the command ```docker run``` for limit the maximum amount of memory of a container. Within the command, specify how much memory you want dedicate to that specific container.

The syntax is the following:

```bash
sudo docker run -it --memory="[memory_limit]" [docker_image]
```

```memory_limit``` should be a positive integer following by the suffix b, k, m o g (short for bytes, kilobytes, megabytes o gigabytes).

For example, to run an instance of a Ubuntu container and configurate a limit to 1GB, the command should be as follow: 

```bash
sudo docker run -it --memory="1g" ubuntu
```

### Defining the amount of memory that a container can swap to disk

Add the option ```swamp``` to store data even after all RAM assigned to the container has been used up. It does this by ignoring the memory limitation and writing directly to the disk. Although this is a useful feature, it is not a recommended practice as it slows down performance.

The option ```swamp``` includes the total amount of non-swap memory plus the amount of swap memory reserved as backup.

The syntax is the following:

```bash
sudo docker run -it --memory="[memory_limit]" --memory-swap="[memory_limit]" [docker_image]
```

For example, to configure ```--memory``` to 1 GB, the amount of swap memory has to be greather than that. To run a container with an additional 1GB of swap memory, you should configurate the option ```swamp``` to 2 GB.

```bash
sudo docker run -it --memory="1g" --memory-swap="2g" ubuntu
```

If you don't want to use swap memory, give ```--memory``` and ```--memory-swap``` the same values.

### Set soft limit to container memory

When you limit the memory access to a container with the option ```--memory``` you are setting a hard limit that the container can't to surpassed. However, you can set a soft limit with the option ```--memory-reserve``` which warns when the container reaches the end of its assigned memory but doesn't stop any of its services.

If ```--memory``` limitations see are not set, setting the soft limit with ```--memory-reservation``` doesn’t completely limit container space. If you have both features enabled, the soft limit is always lower than the maximum space capacity.

For example, if you want that a Ubuntu container had a memory reservation of 750 MB and the maximun RAM capacity of 1 BG, the command is as following:

```bash
sudo docker run -it --memory="1g" --memory-reservation="750m" ubuntu
```

## Limit Container CPU Access

As memory limit, there are different ways to configure CPU limit:

### Defining a CPU limit

The option ```--cpus``` allows you specify how much of the available CPU resources a container can use.

For example, if the host machine has two CPUs and you set ```--cpus="1.5"```, the container is guaranteed at most one and a half of the CPUs.

```bash
sudo docker run -it --cpus="1.5" ubuntu
```

### Assigning dedicated CPU to containers

You can use the option ```--cpuset-cpus``` to limit the specific CPUs or cores a container can use. There are two different formats:

- a comma-separated list. For example: ```0,2,4```
- a hyphen-separated range. For example: ```1-3```

CPUs are addressed using a zero based index. This means 0,2,4 tells Docker to allocate compute-power from the first, third, and fifth CPU.

For example, to run a container y allocate just the second CPU, the command is as following:

```bash
docker run -d --rm --cpuset-cpus 1 ubuntu -c 8 -t 20s
```

Because the set of CPUs is selected explicitly, the container is limited to the capacity of the set.

### CPU Shares for Docker containers  

The option ```--cpu-shares``` allows you to give access to a greater or lesser proportion of the host machine’s CPU cycles. By default, it's configure to 1024. To give a container relatively less CPU time set ```--cpu-shares``` to lower than 1024. To give a container relatively more CPU set ```--cpu-shares``` for Docker run to a value greater than 1024. 

This is only enforced when CPU cycles are constrained. When plenty of CPU cycles are available, all containers use as much CPU as they need. In that way, this is a soft limit. It prioritizes container CPU resources for the available CPU cycles. It does not guarantee or reserve any specific CPU access.

For example, to run a container with less resources shared, the command is as following:

```bash
sudo docker run -it --cpus-shares="700" ubuntu
```

If ```--cpus``` or ```--cpu-quota``` is set then even if there is no contention for CPU time the container will be limited based. This can be good for predictable resource usage but is generally bad for utilisation.

## Sources

- [How to Set Docker Memory and CPU Usage Limit](https://phoenixnap.com/kb/docker-memory-and-cpu-limit)
- [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/#configure-the-default-cfs-scheduler/)
- [CPU Shares for Docker containers](https://www.batey.info/cgroup-cpu-shares-for-docker.html)
- [Docker Container CPU Limits Explained](https://www.thorsten-hans.com/docker-container-cpu-limits-explained)
