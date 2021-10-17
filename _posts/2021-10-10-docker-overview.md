## what is docker

容器技术提供了另一种方式来build、发布和运行应用程序。应用程序构建之后的最终形态是Image，它包含了应用程序运行所需要的所有依赖，使你的应用程序独立于你的运行基础设施。
Docker只是容器运行时（conatiner runtime）的实现之一，同时Docker提供了一系列工具来build、发布和运行应用程序。

[docker overview](https://docs.docker.com/get-started/overview/)

## docker architecture

Docker使用客户端-服务器端（client-server）架构。Docker客户端调用Docker daemon，Docker daemon做所有的打包，运行和发布你的应用程序。Docker客户端可以和Docker Daemon在同一台server上，也可以连接到在远程运行的Docker Daemon。他们利用REST API来进行之间的通信。

![docker architecture](../images/docker/docker-architecture.svg)

### Docker Daemon

Docker Daemon监听客户端的请求来管理Docker的对象， 比如Image，Container，network，volume等。

### Docker Client

Docker提供了2个client， 一个是docker另一个是docker compose。

docker是开发人员与Docker打交道的主要方式。开发人员通过docker来发送指令给Docker Daemon来打包，运行和发布应用程序。

docker compose让开发人员更容易处理比较大的应用，这样的应用通常有多个服务组成。

Containerized Applications
![docker container](../images/docker/container-what-is-container.png)

## Docker objects

1. images

	image是一个只读的模版，包含创建Docker container的指令。通常一个image是基于另一个image创建的，随后添加自己的定制。你可以创建自己的image或者只是使用别人创建好的image， 当自己创建image时，需要创建一个Dockerfile，里面描述了所有的创建image的步骤。
	
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


## Comparing container and virtual machines

![comparing container and virtual machine](../images/docker/vm.png)
