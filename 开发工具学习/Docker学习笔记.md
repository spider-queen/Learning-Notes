### Docker学习笔记

#### 1. Docker是什么？

Docker是一个开源的容器化平台，能够将应用程序、配置文件及其依赖打包成一个可移植的容器，且能够在任何地方运行。容器允许开发者在不同环境中实现一致的应用程序行为，避免传统虚拟机的资源浪费，且启动速度极快。



#### 2. Why Docker？

程序部署和环境配置步骤复杂，项目在开发环境好用但在生产和测试环境中不好用，新同事进项目组需要花费大量时间来配置开发环境等问题... —— Docker可以帮助解决这些问题。



#### 3. Docker和传统虚拟机的区别

| 特性         | Docker                               | 传统虚拟机                                       |
| ------------ | ------------------------------------ | ------------------------------------------------ |
| 架构层级     | 基于容器化技术，容器共享宿主的OS内核 | 基于虚拟化技术，每个虚拟机运行独立的OS           |
| 资源消耗     | 资源消耗较低                         | 资源消耗较高（开OS内核，应用程序和各种基础服务） |
| 启动速度     | 快，通常只需几秒                     | 慢，可能要几分钟                                 |
| 隔离程度     | 进程级别隔离                         | 完全隔离                                         |
| 迁移与便捷性 | 便于迁移和部署，镜像较小             | 迁移复杂，文件大小较大                           |



#### 4. Docker的核心概念

- **容器（Container）**：一个轻量级的、可移植的、可执行的软件包，它包含了运行某个应用所需要的一切，包括代码、运行时、库、环境变量和配置文件。它是基于镜像创建的，与其他容器隔离，共享主机的OS内核。
- **镜像（Image）**：容器的可执行包，一个只读模板，定义了容器的运行环境（比如OS、软件配置等），通过分层存储来优化空间和构建速度。
- **Dockerfile**：文本文件，描述如何自动构建镜像（如指定基础镜像、安装软件、复制文件等）
- **Docker引擎**：Docker的核心服务，负责管理容器的生命周期（包括镜像的创建、容器的运行、网络的配置等）
- **仓库（Registry）**：存储和分发镜像的平台，如Docker Hub（官方公共仓库）和 Harbor（私有仓库）

> 镜像和容器之间的关系类似于Java中的类和实例的关系。类作为一个模板，可以用来创建多个实例，同理，镜像是一个只读的模板，也可以用来创建一个或多个容器（模板的运行实例）



#### 5. Docker的安装

直接去官网：[Docker官网](https://www.docker.com/) 下载对应的安装包即可，安装后记得启动Docker，否则无法进行后续操作。

Windows系统还有一个注意的地方，那就是需要先开启Hyper-V功能，否则Docker无法正常工作。开启的方式是在控制面板中找到程序，搜索Hyper，启用或关闭Windows功能，然后勾选Hyper-V之后，按照提示重启电脑就可以了。 



#### 6. Docker基本使用

- 拉取最新版本的Ubuntu镜像：`docker pull ubuntu:latest`
- 查看本地的Docker镜像：`docker images / docker image ls`
- 运行容器：`docker run nginx（前台运行）` `docker run -d -p 80:80 nginx（后台守护运行+端口映射）`
- 查看正在运行的容器：`docker ps`
- 停止容器：`docker stop <container_id>`
- 删除容器：`docker rm <container_id>`
- 删除镜像：`docker rmi <image_id>`



#### 7. Dockerfile的创建

- 创建一个简单的Dockerfile来构建一个基于Python的镜像：

```dockerfile
# 使用官方的Python基础镜像
FROM python:3.8-slim

# 设置工作目录
WORKDIR /app

# 复制本地的代码到容器内
COPY . /app

# 安装依赖
RUN pip install -r requirements.txt

# 启动命令
CMD ["python", "app.py"]
```

- 构建镜像：`docker build -t my-python-app .`
- 运行容器：`docker run -it my-python-app`



#### 8. Docker Compose简介

Docker Compose 是一个工具，用于定义和运行多容器Docker应用程序。通过 Compose，可以使用 YAML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YAML 文件配置中创建并启动所有服务。

- 创建一个docker-compose.yml文件来定义一个包含Python应用和数据库的应用程序：

```YAML
version: '3'

services:
  web:
    build: .
    ports:
      - "5000:5000"
  db:
    image: postgres:latest
    environment:
      POSTGRES_PASSWORD: example
```

在包含 docker-compose.yml 文件的目录下，使用以下命令启动服务：`docker-compose up`。

这将启动 web 和 db 两个服务，web 服务会构建当前目录下的 Dockerfile 镜像，并映射端口 5000，db 服务会使用官方的 PostgreSQL 镜像，并设置环境变量来配置数据库密码。