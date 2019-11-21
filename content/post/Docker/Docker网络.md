---
title: "Docker网络"
date: 2019-11-20T16:52:13+08:00
draft: false
tags: ["Docker"]
categories: ["Docker"]
---



# docker网络

## overview

Docker’s networking subsystem is pluggable, using drivers. Several drivers exist by default, and provide core networking functionality:

- `bridge`: The default network driver. If you don’t specify a driver, this is the type of network you are creating. **Bridge networks are usually used when your applications run in standalone containers that need to communicate.** See [bridge networks](https://docs.docker.com/network/bridge/).
- `host`: For standalone containers, remove network isolation between the container and the Docker host, and use the host’s networking directly. `host` is only available for swarm services on Docker 17.06 and higher. See [use the host network](https://docs.docker.com/network/host/).
- `overlay`: Overlay networks connect multiple Docker daemons together and enable swarm services to communicate with each other. You can also use overlay networks to facilitate communication between a swarm service and a standalone container, or between two standalone containers on different Docker daemons. This strategy removes the need to do OS-level routing between these containers. See [overlay networks](https://docs.docker.com/network/overlay/).
- `macvlan`: Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network. The Docker daemon routes traffic to containers by their MAC addresses. Using the `macvlan` driver is sometimes the best choice when dealing with legacy applications that expect to be directly connected to the physical network, rather than routed through the Docker host’s network stack. See [Macvlan networks](https://docs.docker.com/network/macvlan/).
- `none`: For this container, disable all networking. Usually used in conjunction with a custom network driver. `none` is not available for swarm services. See [disable container networking](https://docs.docker.com/network/none/).
- [Network plugins](https://docs.docker.com/engine/extend/plugins_services/): You can install and use third-party network plugins with Docker. These plugins are available from [Docker Hub](https://hub.docker.com/search?category=network&q=&type=plugin) or from third-party vendors. See the vendor’s documentation for installing and using a given network plugin.

## bridge

In terms of Docker, a bridge network uses a software bridge which allows containers connected to the same bridge network to communicate, while providing isolation from containers which are not connected to that bridge network.

Bridge networks apply to containers running on the **same** Docker daemon host. 

When you start Docker, a [default bridge network](https://docs.docker.com/network/bridge/#use-the-default-bridge-network) (also called `bridge`) is created automatically, and newly-started containers connect to it unless otherwise specified.You can also create user-defined custom bridge networks. **User-defined bridge networks are superior to the default bridge network.**

[用户定义的bridge网络和default bridge网络的区别](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge):

- **User-defined bridges provide better isolation and interoperability between containerized applications**.

  Containers connected to the same user-defined bridge network automatically expose **all ports** to each other, and **no ports** to the outside world. 

  如果在默认桥接网络上运行相同的应用程序堆栈，则需要打开Web端口和数据库端口，并为每个端口使用-p或--publish标志。这意味着Docker主机需要通过其他方式阻止对数据库端口的访问。

- **User-defined bridges provide automatic DNS resolution between containers**.

  默认网桥上的容器只能通过IP地址相互访问，除非您使用--link选项，这被认为是遗留选项。在用户定义的桥接网络上，容器可以通过名称或别名相互解析。（如果在默认桥接网络上运行相同的应用程序堆栈，则需要在容器之间手动创建链接（使用legacy --link标志）。这些链接需要在两个方向上创建，因此您可以看到这对于需要通信的两个以上容器而言变得复杂。或者，您可以操作容器中的`/etc/hosts`文件，但这会产生难以调试的问题。）

- **Containers can be attached and detached from user-defined networks on the fly**.

  要从默认桥接网络中删除容器，您需要停止容器并使用不同的网络选项重新创建容器。

- **Linked containers on the default bridge network share environment variables**.

  最初，在两个容器之间共享环境变量的唯一方法是使用--link标志链接它们。用户定义的网络无法实现这种类型的变量共享。但是，有更好的方法来共享环境变量：

  - Multiple containers can mount a file or directory containing the shared information, using a Docker volume.
  - Multiple containers can be started together using `docker-compose` and the compose file can define the shared variables.
  - You can use swarm services instead of standalone containers, and take advantage of shared [secrets](https://docs.docker.com/engine/swarm/secrets/) and [configs](https://docs.docker.com/engine/swarm/configs/).

连接到同一用户定义的网桥的容器有效地将所有端口相互暴露。要使端口可以访问不同网络上的容器或非Docker主机，必须使用`-p`或`--publish`标志发布该端口。

## Manage a user-defined bridge

```sh
docker network create my-net
docker network rm my-net
```

