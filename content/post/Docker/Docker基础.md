---
title: "Docker基础"
date: 2019-11-20T16:50:11+08:00
draft: false
tags: ["Docker"]
categories: ["Docker"]
---

# Docker

## 关键概念

### docker engine和docker machine的区别

[官方文档](<https://docs.docker.com/machine/overview/#whats-the-difference-between-docker-engine-and-docker-machine>)

简言之，平时说的docker是指docker engine，其是一个客户端-服务端的应用。而docker machine是一个可以安装docker engine的工具。

### registry

> A registry is a collection of repositories, and a repository is a collection of images。An account on a registry can create many repositories. The `docker` CLI uses Docker’s public registry by default.

## 基本配置问题

1. docker hub镜像加速

   登陆[阿里云](https://cr.console.aliyun.com/undefined/instances/mirrors)，找到镜像中心，镜像加速器，就可以得到加速地址。windows下直接邮件settings即可，在daemon一栏中有Registry mirrors一栏。

2. windows docker share drive，输入**微软账户密码**即可。

3. 注意dockerfile的换行符一定要用`LF`，不然会有奇奇怪怪的问题。

## get start

### orientation

An **image** is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables, and configuration files.

A **container** is a runtime instance of an image--what the image becomes in memory when executed (that is, an image with state, or a user process).

`docker ps`列出正在运行的container。

```shell
## List Docker CLI commands
docker
docker container --help

## Display Docker version and info
docker --version
docker version
docker info

## Execute Docker image
docker run hello-world

## List Docker images
docker image ls

## List Docker containers (running, all, all in quiet mode)
docker container ls
docker container ls --all
docker container ls -aq
```

### container

1. windows下`ctrl-c`不能停止正在运行的容器，需要使用`docker container stop <Container NAME or ID>` 

```dockerfile
docker build -t friendlyhello .  # Create image using this directory's Dockerfile
docker run -p 4000:80 friendlyhello  # Run "friendlyhello" mapping port 4000 to 80（容器内部端口是80，外部4000）
docker run -d -p 4000:80 friendlyhello         # Same thing, but in detached mode(后台运行)
docker container ls                                # List all running containers
docker container ls -a             # List all containers, even those not running
docker container stop <hash>           # Gracefully stop the specified container
docker container kill <hash>         # Force shutdown of the specified container
docker container rm <hash>        # Remove specified container from this machine
docker container rm $(docker container ls -a -q)         # Remove all containers
docker image ls -a                             # List all images on this machine
docker image rm <image id>            # Remove specified image from this machine
docker image rm $(docker image ls -a -q)   # Remove all images from this machine
docker login             # Log in this CLI session using your Docker credentials
docker tag <image> username/repository:tag  # Tag <image> for upload to registry
docker push username/repository:tag            # Upload tagged image to registry
docker run username/repository:tag                   # Run image from a registry
```

### services

```shell
docker stack ls                                            # List stacks or apps
docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
docker service ls                 # List running services associated with an app
docker service ps <service>                  # List tasks associated with an app
docker inspect <task or container>                   # Inspect task or container
docker container ls -q                                      # List container IDs
docker stack rm <appname>                             # Tear down an application
docker swarm leave --force      # Take down a single node swarm from the manager
```

## docker-compose

The Compose file is a [YAML](http://yaml.org/) file defining [services](https://docs.docker.com/compose/compose-file/#service-configuration-reference), [networks](https://docs.docker.com/compose/compose-file/#network-configuration-reference) and [volumes](https://docs.docker.com/compose/compose-file/#volume-configuration-reference). 

> **Tip**: You can use either a `.yml` or `.yaml` extension for this file. They both work.

docker compose文件主要包括三大部分：

- services
- networks
- volumes

> A service definition contains configuration that is applied to each container started for that service, much like passing command-line parameters to `docker container create`. Likewise, network and volume definitions are analogous to `docker network create` and `docker volume create`.

使用`docker compose -f xx.yml up`命令启动，这里`-f`指定当前使用的compose文件名，默认`docker-compose.yml`。

**You can stop your containers using the stop or down command, but the down command does more than stopping your containers.**

The `docker-compose stop` command will stop your containers, but it won’t remove them.

The `docker-compose down` command will stop your containers, but it also removes the stopped containers as well as any networks that were created. 此外多加一个`-v`选项可以把volumes也一同删除。

### restart a single container with docker-compose

```shell
docker-compose restart service_name # restart contrainer without restart the other containers
```

上述命令只会“原样”重启容器，而不会应用更改的任何设置。
可以使用如下命令重新构建某个容器，并应用对配置文件的修改。

```sh
docker-compose up --build service_name
```

### docker-compose up for only certain contains

只需要指定服务名即可。

```sh
docker-compose up -d service_name
```

## Docker Detached Mode

容器默认是`foreground mode`，使用`-d`选项来进入`detached mode`。

>You can start a docker container in detached mode with a `-d` option. So the container starts up and run in background. That means, you start up the container and could use the console after startup for other commands.
>
>The opposite of detached mode is foreground mode. That is the default mode, when `-d` option is not used. In this mode, the console you are using to execute `docker run` will be attached to standard input, output and error. That means your console is attached to the container's process.
>
>In detached mode, you can follow the standard output of your docker container with `docker logs -f <container_ID>`.

ps:

使用`docker container start xx`启动一个已经停止的容器时，默认是detached mode，`-a`选项是foreground mode。





