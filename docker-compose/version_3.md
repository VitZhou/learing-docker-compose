# Compose file Version 3参考文档
## Compose file文件结构和示例
在配置文件中定义一个部分的顶级键(如build,deploy,depends_on,networks等)列出了支持它们作为子主题的选项.这将映射到Comtion文件的<key>:<option>:<value>缩进结构.
一个好的开始是[入门](https://docs.docker.com/get-started/)教程,它使用版本3编写Compose文件来实现多容器应用程序,服务定义和群组模式.以下是本教程中使用的一些Compose文件.
- [你的第一个docker-compose.yml文件](https://docs.docker.com/get-started/part3/#your-first-docker-composeyml-file)
- [添加新服务并重新部署](https://docs.docker.com/get-started/part3/#your-first-docker-composeyml-file)

Docker Labs主题"[将应用程序部署到群集](https://github.com/docker/labs/blob/master/beginner/chapters/votingapp.md)"中包含Compose文件的另一个示例.

## 服务文件参考
Compose文件是一个定义服务,网络和卷的YAML文件.Compose文件的默认路径为./docker-compose.yml.
> 您可以对此文件使用.yml或.yaml扩展名.

服务定义包含将应用于为该服务启动的每个容器的配置,就像将dock-line参数传递给docker运行一样.同样,网络和卷定义类似于docker网络创建和docker卷创建.

与docker运行一样,默认情况下,Dockerfile中指定的选项(例如,CMD,EXPOSE,VOLUME,ENV)都被遵守 - 您不需要在docker-compose.yml中再次指定它们.

您可以使用类似Bash的${VARIABLE}语法在配置值中使用环境变量

本节包含版本3中服务定义支持的所有配置选项的列表.

### build
在构建时应用的配置选项.

构建可以指定为包含构建上下文的路径的字符串,也可以指定具有指定路径的context的对象,可选的dockerfile和args.
```yml
build: ./dir

build:
  context: ./dir
  dockerfile: Dockerfile-alternate
  args:
    buildno: 1
```
如果您指定image和build,则Compose使用image中指定的webapp和可选tag命名内置镜像:
```yml
build: ./dir
image: webapp:tag
```

这将导致从./dir构建的名为webapp和标记tag的镜像.
> 使用(版本3)Compose文件以群组模式部署堆栈时,将忽略此选项.docker stack命令只接受预构建的镜像.

#### CONTEXT
包含Dockerfile目录的路径,或者是git存储库的URL.
当提供的值是相对路径时,它被解释为相对于compose file的位置.此目录也是发送到Docker daemon的build context.
Compose将使用生成的名称构建和标记它,然后使用该镜像.
```yml
build:
  context: ./dir
```

#### dockerfile
备用Dockerfile.
Compose将使用备用文件进行构建.还必须指定构建路径.
```yml
build:
  context: .
  dockerfile: Dockerfile-alternate
```

#### args
添加构建参数,这些环境变量只能在构建过程中访问.
首先,在Dockerfile中指定参数:
```yml
ARG buildno
ARG password

RUN echo "Build number: $buildno"
RUN script-requiring-password.sh "$password"
```
然后在build key下指定参数.您可以传递映射或列表:
```yml
build:
  context: .
  args:
    buildno: 1
    password: secret

build:
  context: .
  args:
    - buildno=1
    - password=secret
```
您可以在指定构建参数时省略该值,在这种情况下,构建时的值是运行Compose的环境中的值.
```yml
args:
  - buildno
  - password
```
> YAML布尔值(true,false,yes,no,on,off)必须用引号括起来,以便解析器将它们解释为字符串.

#### CACHE_FROM
> 这个选项在v3.2中有更新
引擎将用于缓存解析的镜像列表.
```yml
build:
  context: .
  cache_from:
    - alpine:latest
    - corp/web_app:3.14
```

### cap_add,cap_drop
添加或删除容器功能.查看man 7功能的完整列表.
```yml
cap_add:
  - ALL

cap_drop:
  - NET_ADMIN
  - SYS_ADMIN
```
> 使用（版本3）Compose文件在群集模式下部署堆栈时,将忽略这些选项.

### command
覆盖默认的command:
```yml
command: bundle exec thin -p 3000
```
该命令也可以是一个列表,类似于dockerfile:
```yml
command: [bundle, exec, thin, -p, 3000]
```

### cgroup_parent
为容器指定一个可选的父cgroup.
```yml
cgroup_parent: m-executor-abcd
```
> 使用(版本3)Compose文件以群组模式部署堆栈时,将忽略此选项.

### container_name
指定自定义容器名称,而不是生成的默认名称.
```yml
container_name: my-web-container
```
> 使用(版本3)Compose文件以群组模式部署堆栈时,将忽略此选项.因为Docker容器名称必须是唯一的,如果指定了自定义名称.则无法将服务扩展到容器之外.尝试这样做会导致错误.

### deploy
> 只有version 3才有
指定与部署和运行相关的配置.这仅在使用docker stack部署到群集时生效,并由docker-compose和docker-compose run忽略.
```yml
version: '3'
services:
  redis:
    image: redis:alpine
    deploy:
      replicas: 6
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
```
有几个子选项可用：
#### MODE
global(每个群组节点只有一个容器)或者replicated(指定数量的容器),默认值是replicated(要了解更多信息,请参阅[swarm](https://docs.docker.com/engine/swarm/)主题中的[global和replicated服务](https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/#replicated-and-global-services)).
```yml
version: ‘3’
services:
	worker:
    	image: dockersamples/examplevotingapp_worker
        deploy:
        	mode: global
```

#### REPLICAS
如果服务被复制(这是默认值),请指定在任何给定时间应运行的容器数.
```yml
version: '3'
services:
  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 6
```

#### PLACEMENT
指定展示位置约束.有关语法和可用类型的约束的完整描述,请参阅[docker服务创建文档](https://docs.docker.com/engine/reference/commandline/service_create/#specify-service-constraints-constraint).
```yml
version: '3'
services:
  db:
    image: postgres
    deploy:
      placement:
        constraints:
          - node.role == manager
          - engine.labels.operatingsystem == ubuntu 14.04
```

#### UPDATE_CONFIG
配置服务应该如何更新.用于配置服务的滚动更新.
- parallelism:一次更新的容器数.
- delay:更新一组容器之间等待的时间.
- failure_action:更新失败时该继续还是暂停(默认：暂停).
- monitor:每个任务更新后监视失败的持续时间(ns|us|ms|s|m|h)(默认值0s)
- max_failure_ratio:在更新期间最大能容忍的失败率.
```yml
version: '3'
services:
  vote:
    image: dockersamples/examplevotingapp_vote:before
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
        delay: 10s
```

#### RESOURCES
配置资源约束.这将替代版本3之前的Compose文件中的旧资源约束选项(cpu_shares,cpu_quota,cpuset,mem_limit,memswap_limit,mem_swappiness).

每个都是一个单一的值,类似于docker run的对应物.
```yml
version: '3'
services:
  redis:
    image: redis:alpine
    deploy:
      resources:
        limits:
          cpus: '0.001'
          memory: 50M
        reservations:
          cpus: '0.0001'
          memory: 20M
```

#### RESTART_POLICY
配置在退出时是否以及如何重新启动容器.替换restart.
- condition: One of none, on-failure or any (default: any).
- delay: 指定两次尝试重启之间的持续等待时间.(默认是0)
- max_attempts: 抛弃之前尝试重新启动容器的次数(默认值：永不抛弃).
- window: 判定重新启动成功之前等待多长时间,指定为持续时间(默认：立即判定)
```yml
version: "3"
services:
  redis:
    image: redis:alpine
    deploy:
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
```

#### LABELS
指定服务的标签.这些标签只能在服务上设置,而不能在服务的任何容器上设置
```yml
version: "3"
services:
  web:
    image: web
    deploy:
      labels:
        com.example.description: "This label will appear on the web service"
```
要在容器上设置标签,请使用deploy以外的标签键:
```yml
version: "3"
services:
  web:
    image: web
    labels:
      com.example.description: "This label will appear on all containers for the web service"
```

#### 不支持DOCKER STACK DEPLOY
docker stack deploy或deploy key不支持以下子选项(支持docker组合和docker组合运行).
- build
- cgroup_parent
- container_name
- devices
- dns
- dns_search
- tmpfs
- external_links
- links
- network_mode
- security_opt
- stop_signal
- sysctls
- userns_mode

### devices
设备映射列表使用与--device docker客户端创建选项相同的格式.
```yml
devices:
  - "/dev/ttyUSB0:/dev/ttyUSB0"
```
> 使用（版本3）Compose文件以群组模式部署堆栈时,将忽略此选项.

### depends_on
服务之间的快速依赖,这有两个作用:
- docker-compose up 将依次顺序启动服务.在以下示例中,db和redis将在Web之前启动.
- docker-compose up SERVICE 将自动包含SERVICE的依赖关系.在以下示例中,docker-compos up web也将创建并启动db和redis.

```yml
version: '3'
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```
> 使用depends_on时需要注意的几件事情
- depends_on不会在启动Web之前等待db和redis“ready”,直到它们被启动为止.如果您需要等待服务准备就绪,请参阅控制启动顺序以了解有关此问题的更多信息以及解决此问题的策略。
- 版本3不再支持depends_on的条件形式.
- 使用（版本3）Compose文件以群组模式部署堆栈时,将忽略此选项.

### dns
自定义DNS服务器.可以是单个值或列表.
```yml
dns: 8.8.8.8
dns:
  - 8.8.8.8
  - 9.9.9.9
```
> 使用（版本3）Compose文件以群组模式部署堆栈时,将忽略此选项.

### dns_search
自定义DNS搜索域.可以是单个值或列表.
```yml
dns_search: example.com
dns_search:
  - dc1.example.com
  - dc2.example.com
```
> 使用（版本3）Compose文件以群组模式部署堆栈时,将忽略此选项.

在容器内安装临时文件系统.可以是单个值或列表.
```yml
tmpfs: /run
tmpfs:
  - /run
  - /tmp
```
> 使用（版本3）Compose文件以群组模式部署堆栈时,将忽略此选项.

### entrypoint
覆盖默认entrypoint
```yml
entrypoint: /code/entrypoint.sh
```
entrypoint也可以是一个列表,类似于dockerfile:
```yml
entrypoint:
    - php
    - -d
    - zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
    - -d
    - memory_limit=-1
    - vendor/bin/phpunit
```
> 设置entrypoint将使用ENTRYPOINT Dockerfile指令覆盖服务镜像上的任何默认入口点集,并清除镜像上的任何默认命令 - 这意味着如果Dockerfile中有一个CMD指令,将被忽略.

### env_file
从文件添加环境变量.可以是单个值或列表.
如果您使用docker-compose -f FILE指定了一个Composefile,则env_file中的路径与文件所在的目录相关.
环境中指定的环境变量覆盖这些值.
```yml
env_file: .env

env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/secrets.env
```
Compose希望env文件中的每一行都是VAR=VAL格式.以＃(即注释)开头的行将被忽略,空白行也将被忽略.
```xml
# Set Rails/Rack environment
RACK_ENV=development
```
> 如果您的服务指定了一个构建选项,则在构建期间,环境文件中定义的变量将不会自动显示.使用构建的args子选项定义构建时环境变量.

VAL的值按原样使用,完全不修改.例如,如果值被引号包围(通常是shell变量的情况),包含在引号内的值将传递给Compose.