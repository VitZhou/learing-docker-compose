# docker-compose cli概述
此页面提供了docker-compose Command的使用信息.您还可以通过从命令行运行docker-compose --help来查看此信息.
使用Docker定义和运行多容器应用程序.
- 用法:
	- docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
	- docker-compose -h|--help
- Options(选项):
	- -f, --file FILE: 指定一个备用文件（默认值：docker-compose.yml）
	- -p, --project-name NAME: 指定备用项目名称（默认值：project name）
	- --verbose: 显示更多输出
	- -v, --version: 打印版本并退出
	- -H, --host HOST: Docker Daemon链接端口
	- --tls: 使用TLS; 暗示--tlsverify
	- --tlscacert CA_PATH: 信托证书仅由本CA签署
	- --tlscert CLIENT_CERT_PATH: TLS证书文件的路径
	- --tlskey TLS_KEY_PATH: TLS密钥文件的路径
	- --tlsverify: 使用TLS并远程验证
	- --skip-hostname-check: 不要根据客户端证书中指定的名称检查Docker Daemon的主机名（例如,如果您的Docker主机是IP地址）
	- --project-directory PATH: 指定备用工作目录（默认：Composefile的路径）
- Commands(命令):
	- build: 构建或者重新构建服务
	- bundle: 从Compose文件生成Docker包
	- config: 验证并查看Composefile
	- create: 创建服务
	- down: 停止并删除容器,网络,镜像和挂载卷
	- events: 从容器接收事实时间
	- exec: 在运行的容器中执行命令
	- help: 获取command帮助
	- images: 镜像列表
	- kill: 杀掉容器
	- logs: 查看容器的日志输出
	- pause: 暂停服务
	- port: 打印端口绑定的公共端口
	- ps: 容器列表
	- pull: 拉取服务镜像
	- push: push服务镜像
	- restart: 服务重启
	- rm: 删除已停止的容器
	- run: 运行一次性command
	- scale: 设置服务的容器数
	- start: 启动服务
	- stop: 停止服务
	- top: 显示正在运行的进程
	- unpause: 取消服务暂停
	- up: 创建并且启动容器
	- version: 显示docker compose版本信息

Docker Compose二进制文件.您可以使用此命令在Docker容器中构建和管理多个服务.
使用-f标志来指定Compose配置文件的位置.您可以提供多个-f配置文件.当您提供多个文件时,Compose将它们组合成单个配置.Compose以您提供文件的顺序构建配置.后续文件覆盖并添加到其前身.
例如,考虑这个命令行:
```shell
$ docker-compose -f docker-compose.yml -f docker-compose.admin.yml run backup_db
```
docker-compose.yml文件可能指定一个webapp服务.
```yml
webapp:
  image: examples/web
  ports:
    - "8000:8000"
  volumes:
    - "/data"
```
如果docker-compose.admin.yml也指定了相同的服务,任何匹配的字段都将覆盖以前的文件.新值会添加到webapp服务配置.
```yml
webapp:
  build: .
  environment:
    - DEBUG=1
```
使用-f与-(破折号)作为文件名从stdin读取配置.当使用stdin时,配置中的所有路径都相对于当前工作目录.
-f标志是可选的.如果您不在命令行中提供此标志,Compose遍历工作目录及其父目录,寻找docker-compose.yml和docker-compose.override.yml文件.必须至少提供docker-compose.yml文件.如果两个文件都存在于同一目录级别,Compose将两个文件组合成一个配置.docker-compose.override.yml文件中的配置应用于docker-compose.yml文件中的值,并附加在docker-compose.yml文件中.

每个配置都有一个项目名称.如果提供-p标志,则可以指定项目名称.如果不指定标志,Compose使用当前目录名.