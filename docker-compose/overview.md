# Docker Compose概述
Compose是定义和允许多容器Docker应用的工具.使用Compose,你可以使用Compose文件来配置应用的服务.然后使用单个命令,你可以从配置中创建并启动所有服务.

Compose适用于开发,测试和不同环境以及ci工作流程

使用Compose基本上只需要三个步骤:
1.  使用Dockerfile定义你的应用的环境,以便可以在任何地方进行复制.
2.  在Docker-compose.yml中定义构成你应用程序的服务,以便它们可以在独立的环境中一运行.
3.  最后,运行docker-compose,Compose将启动并运行你的整个应用.

docker-compose.yml看起来像这样:
```yml
version: '3'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```
ompose具有管理应用程序整个生命周期的命令:
- start,stop和rebuild服务.
- View: 查看运行服务的状态.
- Stream: 流式运行服务的日志输出
- Run: 一次性服务命令

## Features
Compose的主要特性有:
- 单个主机上的多个独立环境
- 创建容器时保留卷数据
- 只重复创建已更改的容器
- 变量和移动环境之间的组合

### 单个主机上的多个独立环境
Compose使用项目名称来隔离环境.你可以在几个不同的上下文中使用此项目名称:
- 在开发主机上穿件单个环境的多个副本(例如,要为项目的每个功能功能运行稳定的副本)
- 在ci服务器上,为了保持构建互不干扰,你可以将项目名称设置为唯一的内部版本号
- 在共享主机或者开发者主机上,防止可能使用相同服务名称的不同项目彼此干扰.
默认项目名称是项目目录的basename.你可以使用-p命令或者COMPOSE_PROJECT_NAME环境变量来设置自定义项目名称.

### 创建容器时保留volume数据
Compose保留你的服务使用的所有volume.当docker-compos up run时,如果从以前的运行中找到任何容器,则将volume复制到新容器.此过程确保你的volume中创建的任何数据都不会丢失.

如果在Windows机器上使用docker-compose,请参阅环境变量并根据具体需要调整必要的[环境变量](https://docs.docker.com/compose/reference/envvars/).

### 只重新创建已更改的容器
Compose缓存用于创建容器的配置.当你重启未改动的容器时,compose会重新使用现有的容器.重新使用容器意味着您可以很快地对您的环境进行更改.

### 变量和移动环境之间的组合
Compose支持Compose文件中的变量.你可以使用这些变量来定制不同环境或不同用户的组合.
您可以使用扩展字段或通过创建多个Compose文件来扩展Compose文件

## 常用案例
Compose可以以许多不同的方式使用.下面概述了一些常见的用例.
### 开发环境
在开发软件时,在独立环境中运行应用程序并与之交互的能力至关重要.Compose命令行工具可用于创建环境并与之进行交互.
Compose文件提供了一种记录和配置所有应用程序的服务依赖关系（数据库,队列,缓存,Web服务API等）的方法.使用Compose命令行工具,您可以使用单个命令（docker-compose up）为每个依赖关系创建和启动一个或多个容器.
这些功能一起为开发人员开始一个项目提供了便利的方式.Compose可以将多页面的“开发人员入门指南”减少到单个可读写入文件和几个命令.

### 自动测试环境
任何持续部署或持续集成过程的重要组成部分是自动测试套件.自动端到端测试需要运行测试的环境. Compose为您的测试套件创建和销毁隔离的测试环境提供了一种便捷的方法.通过在Compose文件中定义完整的环境,您可以在几个命令中创建和销毁这些环境：
```shell
$ docker-compose up -d
$ ./run_tests
$ docker-compose down
```

### 单台主机部署
传统上,Compose一直专注于开发和测试工作流程,但是随着每个版本的推出,我们都将在更多面向生产的功能上取得进展.您可以使用Compose部署到远程Docker Engine.Docker Engine可能是使用[Docker Machine](https://docs.docker.com/machine/overview/)或整个[Docker Swarm](https://docs.docker.com/swarm/overview/)群集提供的单一实例.