# Options详情
## build
用途: 服务已构建一次,然后标记为project_service,例如composetest_db.如果您更改服务的Docker文件或其构建目录的内容,请运行docker-compose build来重建它.
用法: build [options] [--build-arg key=val...] [SERVICE...]
options:
- --force-rm: 总是删除中间容器
- --no-cache: 构建镜像时不使用缓存
- --pull: 始终尝试拉一个较新版本的镜像
- --build-arg key=val:为一个服务设置构建时变量


## bundle
用途: 从Compose file生成分布式应用程序包（DAB）.
镜像必须存储摘要,这需要与docker注册表进行交互.如果没有为所有镜像存储摘要,你可以使用docker-compose pull或者docker-compose push来获取它们.--push-images,bundling时自动推送镜像,只有指定了构建选项的服务才能推送镜像.
用法: bundle [options]
options:
- --push-images: 自动推送具有指定“build”选项的任何服务的镜像
- -o, --output PATH: 将bundle文件写入的路径.默认为"<project name>.dab".

## config
用途: 验证并查看compose file.
用法: config [options]
options:
- --resolve-image-digests: 引导镜像标签摘要.
- -q, --quiet: 只验证配置,不要打印任何东西.
- --services: 打印服务名称,每行一个.
- --volumes: 打印挂载卷名称,每行一个.

## create
用途: 创建服务的容器
用法: create [options] [SERVICE...]
options:
- --force-recreate: 即使他们的配置和镜像没有改变,也可以重新创建容器.与--no-recreate不兼容.
- --no-recreate: 如果容器已经存在,不要重新创建它们.与--force-recreate不兼容.
- --no-build: 不要建立一个镜像,即使它没有.
- --build: 创建容器之前构建镜像.

## down
用途: 停止容器并移除由up创建的容器,网络,挂载卷和镜像.
默认情况下,唯一删除的内容是:
- Compose文件中定义的服务容器
- 在Compose文件的网络部分中定义的网络
- 默认网络,如果使用
定义为external的网络和卷不会被删除.
用法: down [options]
options:
- --rmi type: 删除镜像.类型必须是以下之一:
    - all: 删除任何服务使用的所有镜像.
    - local: 只删除没有“image”字段设置的自定义标签的镜像
- -v, --volumes: 删除在Compose文件的“volumes”部分中声明的命名卷和附加到容器的匿名卷.
- --remove-orphans: 删除未在compose文件中定义的服务的容器

## event
用途: 项目中每个容器中的容器事件流.
使用--json标志,一行json对象将按照以下格式打印一行:
```json
{
    "service": "web",
    "event": "create",
    "container": "213cf75fc39a",
    "image": "alpine:edge",
    "time": "2015-11-20T18:01:03.615550",
}
```
用法: events [options] [SERVICE...]
options:
- --json: 输出事件作为json对象流

## exec
用途: 这相当于docker exec,使用此子命令,您可以在服务中运行任意命令.默认情况下,命令分配一个TTY,所以你可以这样做.docker-compose exec web sh获取交互式提示
用法: exec [options] SERVICE COMMAND [ARGS...]
options:
- -d: 分离模式:在后台运行命令.
- --privileged: 给进程扩展特权.
- --user USER: 以此用户身份运行命令
- -T: 禁用伪tty分配.默认情况下`docker-compose exec`分配一个TTY.
- --index=index: 容器的索引如果有多个服务实例[default：1]

## kill
用法: kill [options] [SERVICE...]
options:
- -s SIGNAL: SIGNAL发送到容器.默认信号是SIGKILL

通过发送SIGKILL信号来强制运行容器停止.传递信号是可选的.例如：
```shell
docker-compose kill -s SIGINT
```

## logs
用途: 显示服务的日志输出.
用法: logs [options] [SERVICE...]
options:
- --no-color: 生产单色输出.
- -f, --follow: 输出跟踪日志
- -t, --timestamps: 显示时间戳
- --tail="all": 在每个容器的日志末尾显示行数.

## pause
用途: 暂停运行服务的容器.可以使用docker-compose unpause取消暂停.
用法: pause [SERVICE...]

## port
用途: 打印端口绑定的公共端口
用法: port [options] SERVICE PRIVATE_PORT
options:
- --protocol=proto: tcp或udp[默认:tcp]
- --index=index: 容器的索引,如果有多个服务实例[default：1]

## ps
用途: 容器列表,例如:
```shell
$ docker-compose ps
```
用法: ps [options] [SERVICE...]
options:
- -q: 只显示ID

## pull
用途: 拉取服务镜像
用法: pull [options] [SERVICE...]
options:
- --ignore-pull-failures: 忽略失败的拉取镜像
- --parallel: 并行拉取多个镜像

## push
用法: push [options] [SERVICE...]
options:
- --ignore-push-failures: 忽略失败推送服务镜像

用途: 将服务的镜像推送到其各自的注册表/存储库.
做出以下假设:
- 你正在推动你在本地建立的镜像
- 您可以访问构建密钥

例如:
```yml
version: '3'
services:
  service1:
    build: .
    image: localhost:5000/yourimage  # goes to local registry

  service2:
    build: .
    image: youruser/yourimage  # goes to youruser DockerHub registry
```

## restart
用途: 重新启动所有停止和正在运行的服务.
如果您对docker-compose.yml配置进行了更改,则运行此命令后,这些更改将不会被反映.
例如,在重新启动之后,环境变量的更改(在构建容器后,但在容器的命令执行之前添加的)将不会更新.
用法: restart [options] [SERVICE...]
options:
- -t, --timeout TIMEOUT: 指定超时关闭(以秒为单位).(默认值：10)

## rm
用法: rm [options] [SERVICE...]
options:
- -f, --force: 删除不要求确认
- -s, --stop: 如果需要,在删除前停止容器
- -v: 删除附加到容器的任何匿名卷

用途: 删除已停止的服务容器.
默认情况下,连接到容器的匿名卷将不会被删除.你可以用-v覆盖它.要列出所有卷,请使用docker volume ls.
任何不在卷中的数据都将丢失.
运行没有选项的命令也将删除由docker-compost或docker-compose运行创建的一次性容器:
```shell
$ docker-compose rm
Going to remove djangoquickstart_web_run_1
Are you sure? [yN] y
Removing djangoquickstart_web_run_1 ... done
```

## run
用法: run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]
options:
- -d: 分离模式:在后台运行容器,打印新的容器名称.
- --name NAME: 为容器指定名称
- --entrypoint CMD: 覆盖镜像的入口点
- -e KEY=VAL: 设置一个环境变量（可以多次使用）
- -u, --user="": 以指定的用户名或uid身份运行
- --no-deps: 不要启动已link服务
- --rm: 运行后删除容器.在分离模式下忽略.
- -p, --publish=[]: 将容器的端口发布到宿主机
- --service-ports: 运行命令,启用服务端口并映射到主机
- -v, --volume=[]: 绑定一个挂载卷（默认为[]）
- -T: 禁用伪tty分配.默认情况下'docker-compose run'分配一个TTY.
- w, --workdir="": 容器内的工作目录

对服务执行一次性命令.例如,以下命令启动Web服务并运行bash作为其命令.
```shell
docker-compose run web bash
```
您使用的命令与新服务器中的运行启动一起使用,由配置定义的服务包括卷,链接和其他详细信息.但是,有两个重要的区别.
首先,通过运行传递的命令将覆盖服务配置中定义的命令.例如,如果Web服务配置以bash启动,则docker-compose运行web python app.py将使用python app.py来覆盖它.
第二个区别是docker-compose run命令不会创建服务配置中指定的任何端口.这可以防止与已打开端口的端口冲突.如果您希望创建服务时配置端口并将其映射到主机,请指定--service-ports标志:
```shell
docker-compose run --service-ports web python manage.py shell
```
或者,可以使用--publish或-p选项指定手动端口映射,就像使用docker运行一样:
```shell
docker-compose run --publish 8080:80 -p 2022:22 -p 127.0.0.1:2021:21 web python manage.py shell
```
如果您启动配置了链接的服务,则运行命令首先检查链接服务是否正在运行,如果停止,则启动该服务.一旦所有链接的服务都在运行,运行将执行您传递的命令.例如,您可以运行:
```shell
docker-compose run db psql -h db -U docker
```
这将为链接的数据库容器打开一个交互式PostgreSQL shell.
如果不希望运行命令启动链接容器,请使用--no-deps标志:
```shell
docker-compose run --no-deps web python manage.py shell
```

## scale
用处: 设置要为服务运行的容器数.
数字被指定为参数,格式为service = num.例如:
```shell
docker-compose scale web=2 worker=3
```
用法: scale [SERVICE=NUM...]

## start
用法: start [SERVICE...]
启动一个服务的现有容器.

## stop
用法: stop [options] [SERVICE...]
options:
- -t, --timeout TIMEOUT: 指定超时关闭（以秒为单位）（默认值：10）.
用处: 停止运行容器而不删除它们.他们可以用docker-compose start再次启动.

## top
用法: top [SERVICE...]
显示正在运行的进程:
```shell
$ docker-compose top
compose_service_a_1
PID    USER   TIME   COMMAND
----------------------------
4060   root   0:00   top

compose_service_b_1
PID    USER   TIME   COMMAND
----------------------------
4115   root   0:00   top
```

## unpause
用途: 取消暂停服务的容器.
用法: unpause [SERVICE...]

## up
用法: up [options] [SERVICE...]
options:
- -d: 分离模式:在后台运行容器,打印新的容器名称.与--abort-on-container-exit不兼容
- --no-color: 生产单色输出
- --no-deps: 不要启动已link服务
- --force-recreate: 即使他们的配置和镜像没有改变,也可以重新创建容器.与--no-recreate不兼容
- --no-recreate: 如果容器已经存在，请不要重新创建它们.与--force-recreate不兼容.
- --no-build: 不要建立镜像,即使它没有
- --build: 启动容器之前构建镜像
- --abort-on-container-exit: 任何容器停止时,都会停止所有容器.与-d不兼容
- -t, --timeout TIMEOUT: 在连接时或容器已在运行时,使用此超时时间才能进行容器关闭.(默认值:10)
- --remove-orphans: 删除未在Compose file中定义的服务的容器
- --exit-code-from SERVICE: 返回所选服务容器的退出code,意味着会执行--abort-on-container-exit

### 用途
构建,(重新)创建,启动和附加到服务的容器.
除非它们已经在运行,否则此命令也将启动任何已链接的服务.
docker-compose up命令聚合每个容器的输出.当命令退出时,所有容器都将停止.运行docker-compos up -d在后台启动容器并使其运行.
如果存在服务的现有容器,并且服务的配置或镜像在容器创建之后已更改,则docker-compos up将通过停止并重新创建容器(保存挂载的卷)来选择更改.要防止Compose接收更改,请使用--no-recreate标志.
如果要强制Compose停止并重新创建所有容器,请使用--force-recreate flag.

