## what is docker

容器技术提供了另外一种方式来build、发布和运行应用程序。应用程序构建之后的最终形态是Image，它包含了应用程序运行所需要的所有依赖，使你的应用程序独立于你的运行基础设施， Image通过发布到Registry中，当要运行我们的应用程序时，就可以直接从Registry中下载到我们的主机中，然后运行。当发现我们的应用需要更多的实例来响应用户的请求时，我们可以在其他的主机中迅速的启动我们的应用程序，我们不用担心是否这台主机包含了应用程序所需要的依赖，因为我们的Image里已经包含了运行所需要的一切，这也是为什么我们称之为容器了。
Docker只是容器运行时（conatiner runtime）的实现之一，同时Docker提供了一系列工具来build、发布和运行应用程序。

[docker overview](https://docs.docker.com/get-started/overview/)

## docker architecture

Docker使用客户端-服务器（client-server）架构。Docker客户端调用Docker daemon，Docker daemon做所有的打包，运行和发布你的应用程序。Docker客户端可以和Docker Daemon在同一台server上，也可以连接到在远程运行的Docker Daemon。他们利用REST API来进行之间的通信。

![docker architecture](../images/docker/docker-architecture.svg)

### Docker Daemon

Docker Daemon监听客户端的请求来管理Docker的对象， 比如Image，Container，network，volume等。

### Docker Client

Docker提供了2个client， 一个是docker另一个是docker compose。

docker是开发人员与Docker打交道的主要方式。开发人员通过docker来发送指令给Docker Daemon来打包，运行和发布应用程序。

docker compose让开发人员更容易处理比较大的应用，这样的应用通常有多个服务组成。

### Docker Registry

Docker registry存储Docker Images. Docker hub 是一个公共的，任何人都可以用的Registry. Docker默认用这个Registry去下载Images.

## Docker Objects

1. images

	image是一个只读的模版，包含创建Docker container的指令。通常一个image是基于另一个image创建的，随后添加自己的定制。你可以创建自己的image或者只是使用别人创建好的image， 当自己创建image时，需要创建一个Dockerfile，里面描述了所有的创建image的步骤。下面是一个样例Dockerfile。
	
	
```docker
# syntax=docker/dockerfile:1
FROM node:12-alpine
RUN apk add --no-cache python g++ make
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```

2. containers

	容器是Image的运行实例，你可以利用Docker API或者CLI来创建、删除、启动、关闭容器。一台机器可以同时运行多个容器，容器与容器之间以及容器与宿主机之间具有很好的隔离。
	

## Comparing container and virtual machines

| Container | VM |
| ---- | ---- |
|![docker container](../images/docker/container-what-is-container.png)|![virtual machine](../images/docker/vm.png)|

Container共享宿主机操作系统内核，而VM是对硬件的虚拟化，VM中运行一个独立的操作系统。Container相比VM，它更轻量，更高效，消耗更少的系统资源。

## 底层技术

Docker使用Go语言写的，利用了linux内核的namespace和cgroup技术来提供容器之间的隔离和资源的控制。

## 进一步阅读

1. [安装Docker](https://docs.docker.com/get-docker/)
2. [Docker入门](https://docs.docker.com/get-started/)


