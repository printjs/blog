[首页](https://printjs.github.io/blog) / [go语言](https://printjs.github.io/blog/docs/docker) / 开始

# 开始

##方向

1.设置你的docker环境。
2.建立一个镜像并且将它作为一个容器运行。
3.扩展您的应用程序以运行多个容器
4.在群集中分发您的应用
5.通过添加后端数据库来堆栈服务
6.将您的应用部署到生产环境

### docker概念

Docker是开发人员和系统管理员 使用容器开发，部署和运行应用程序的平台。使用Linux容器部署应用程序称为容器化。容器不是新的，但它们用于轻松部署应用程序。

容器化越来越受欢迎，因为容器是：

灵活：即使是最复杂的应用也可以集装箱化。
轻量级：容器利用并共享主机内核。
可互换：您可以即时部署更新和升级。
便携式：您可以在本地构建，部署到云，并在任何地方运行。
可扩展：您可以增加并自动分发容器副本。
可堆叠：您可以垂直和即时堆叠服务。 

### 容器和镜像

通过运行镜像启动容器。一个镜像是一个可执行的包，其中包括运行应用程序所需的所有内容-的代码，运行时，库，环境变量，和配置文件。

容器是已经在内存中运行的镜像的实例。您可以使用该命令查看正在运行的镜像列表docker ps，就像在Linux中一样。

### 容器和虚拟机

一个容器中运行原生 Linux和共享主机与其它容器的内核。它运行一个独立的进程，不占用任何其他可执行文件的内存，使其轻量级。

相比之下，虚拟机（VM）运行一个完整的“客户”操作系统，通过虚拟机管理程序对主机资源进行虚拟访问。通常，VM提供的环境比大多数应用程序需要的资源更多

<img src="./images/container1.png" />
<img src="./images/vm.png" />


## 容器

### 使用Dockerfile文件定义容器

```shell
# Use an official Python runtime as a parent image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

### 构建应用程序

* 构建镜像 `docker build -t=<image name>`
* 运行镜像 `docker run -p 4000:80 <image name>`
* 查看镜像 `docker image ls`
* 查看容器 `docker container ls`
* 停止容器 `docker container stop <Container NAME or ID>`
* 重启docker服务 `sudo serivce docker restart`

### 登录并且上传镜像

## 服务

### docker-compose.yml

`docker-compose.yml`文件是一个yaml文件，它定义了容器在生产中的表现

```yml
version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image: username/repo:tag
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "4000:80"
    networks:
      - webnet
networks:
  webnet:
```

该`docker-compose.yml`文件告诉docker执行以下操作

* 拉我们在步骤2中上传的图像从注册表。

* 将该映像的5个实例作为一个被调用的服务运行web，限制每个实例使用，最多只占单个CPU核心时间的10％（这也可以是例如“1.5”表示每个核心的1和半核心），以及50MB RAM。

* 如果一个失败，立即重启容器。

* 将主机上的端口4000映射到web端口80。

* 指示web容器通过称为负载平衡的网络共享端口80 webnet。（在内部，容器本身web在短暂的端口发布到 80端口。）

* webnet使用默认设置（负载平衡的覆盖网络）定义网络。

### 运行新的负载均衡应用

* 初始化集群节点 `docker swarm init`
* 运行应用程序 `docker stack deploy -c docker-compose.yml <application name>`
* 启动完成后，成为了服务,查看服务`docker service ls`
* 查看服务任务`docker service ps <application name>`


## 集群

### 创建虚拟机

```docker
docker-machine create --driver virtualbox myvm1
docker-machine create --driver virtualbox myvm2
```

列出虚拟机`docker-machine ls`

初始化SWARM并且添加节点
```docker
$ docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"
Swarm initialized: current node <node ID> is now a manager.

To add a worker to this swarm, run the following command:

  docker swarm join \
  --token <token> \
  <myvm ip>:<port>

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```
