---
title: "Docker Redis"
date: 2019-11-20T16:54:06+08:00
draft: false
tags: ["Docker"]
categories: ["Docker"]
---

# docker redis

## 拉取镜像和运行容器

```bash
docker pull redis
```

默认拉取`latest`的tag。

```sh
docker run -d -p 6379:6379 --name 容器名 redis
```

创建容器并运行，6379是Redis的默认端口。

## 在容器中启动redis-cli中连接redis

```sh
docker exec -it 容器名 sh
```

exec在指定容器中执行命令，该命令新建一个交互式会话，`-i`表示interactive，`-t`表示启动一个终端。这里`sh`是在容器中要执行的命令。

启动好shell之后，输入`redis-cli`即可连接。

连接好之后，输入ping会返回pong：

```sh
127.0.0.1:6379> ping
PONG
```

输入`exit`即可退出。（需要exit两次，一次是`redis-cli`，一次是`shell`）

## 从另一个容器中使用redis-cli连接redis

首先创建docker网络，默认使用bridge网络：

```sh
docker network create my_app
docker run --net my_app -d --name redis redis
```

然后启动一个新容器，连接到创建的网络。

```shell
docker run -it --rm --net my_app redis redis-cli -h redis
```

这里`-h`不是docker的`--hostname`，Defaults to the container’s ID if not specified.而是redis-cli命令的参数，指定主机，这里必须加上这个选项。



