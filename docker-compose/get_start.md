# 开始使用Compose
在此页面上,您将构建一个在Docker Compose上运行的简单的Python Web应用程序.该应用程序使用Flask框架,并在Redis中维护一个命中计数器.虽然示例使用Python,但这里展示的概念应该是可以理解的,即使您不熟悉它.

确保您已经安装了Docker Engine和Docker Compose.您不需要安装Python或Redis,因为这两个都是由Docker镜像提供的.

## Setup
1. 为项目创建一个目录:
```shell
$ mkdir composetest
$ cd composetest
```
1. 在您的项目目录中创建一个名为app.py的文件,并将下内容复制进去:
```python
    from flask import Flask
    from redis import Redis

    app = Flask(__name__)
    redis = Redis(host='redis', port=6379)

    @app.route('/')
    def hello():
    count = redis.incr('hits')
    return 'Hello World! I have been seen {} times.\n'.format(count)

    if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```

1. 在项目目录中创建一个名为requirements.txt的另一个文件,并将下内容复制进去:
```txt
flask
redis
```
这些都是在定义程序的依赖关系

## 创建Dockerfile
在此步骤中,您将编写一个构建Docker镜像的Dockerfile.该图像包含Python应用程序所需的所有依赖项,包括Python本身.
在您的项目目录中,创建一个名为Dockerfile的文件并粘贴以下内容:
```dockerfile
FROM python:3.4-alpine
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```
这告诉Docker：
从Python 3.4映像开始构建一个镜像.
添加当前目录.进入镜像中的/code路径.
将工作目录设置为/code.
安装Python依赖关系.
将容器的默认命令设置为python app.py.

## 在Compose file中定义服务
在项目目录中创建一个名为docker-compose.yml的文件,并粘贴以下内容:
```yml
version: '2'
services:
  web:
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    image: "redis:alpine"
```
此Compose文件定义了两个服务,即web和redis.该web服务:
- 使用当前目录中的Dockerfile构建镜像
- 将容器上暴露的5000端口转发到宿主机上的5000端口
- 将宿主机上的项目目录挂载到容器内的/code中,以便允许修改代码,而无需重新构建映像

redis服务使用从Docker Hub注册表提取的公共Redis镜像.
>如果您的项目在用户目录（cd〜）之外,则需要共享您正在使用的Dockerfile和volume的驱动器或位置.如果出现指示未找到应用程序文件的运行时错误,则会拒绝volume挂载,否则服务无法启动,请尝试启用文件或驱动器共享.

## 使用Compose构建和运行您的应用程序
1. 在项目目录,启动应用程序.
```shell
 $ docker-compose up
 Pulling image redis...
 Building web...
 Starting composetest_redis_1...
 Starting composetest_web_1...
 redis_1 | [8] 02 Jan 18:43:35.576 # Server started, Redis version 2.8.3
 web_1   |  * Running on http://0.0.0.0:5000/
 web_1   |  * Restarting with stat
```
Compose提取Redis映像,为代码构建映像,并启动您定义的服务.

1. 在浏览器中输入http://0.0.0.0:5000/查看应用程序的运行.
如果您在Linux上使用Docker,那么Web应用程序现在应该在Docker daemon程序主机上监听5000端口.如果http://0.0.0.0:5000无法解决,还可以尝试http://localhost:5000.
如果您在Mac上使用Docker Machine,请使用docker-machine ip MACHINE_VM获取Docker主机的IP地址.然后在浏览器中打开http://MACHINE_VM_IP:5000.
您应该在浏览器中看到一条消息:
Hello World! I have been seen 1 times.

1. 刷新页面
数字应该增加

## 更新应用
因为使用volume将应用程序代码安装到容器中,您可以更改其代码并立即查看更改,而无需重新构建映像.
更改app.py中的问候语并保存.例如:
```python
return 'Hello from Docker! I have been seen {} times.\n'.format(count)
```
在浏览器中刷新应用程序.应该看到更新的问候语,计数器应该继续递增.
>如果您在较旧的Windows操作系统上使用Oracle VirtualBox,则可能会遇到此VB故障单中所述的共享文件夹问题.较新的Windows系统满足Docker for Windows的要求,不需要VirtualBox.

## 尝试一些其他命令
如果要在后台运行服务,可以将-d标志（“分离”模式）传给docker,并使用docker-compose ps来查看当前运行的内容:
```shell
$ docker-compose up -d
Starting composetest_redis_1...
Starting composetest_web_1...

$ docker-compose ps
Name                 Command            State       Ports
-------------------------------------------------------------------
composetest_redis_1   /usr/local/bin/run         Up
composetest_web_1     /bin/sh -c python app.py   Up      5000->5000/tcp
```
docker-compose运行命令允许您为您的服务运行一次性命令.例如,要查看Web服务可用的环境变量:
```shell
$ docker-compose run web env
```
docker-compose --help查看其他可用的命令.您还可以为bash和zsh shell安装命令完成,这也将显示可用的命令.

如果您使用docker-compos up -d开始撰写，您可能希望在完成后停止您的服务:
```shell
$ docker-compose stop
```

你可以把所有的东西都down,用下来的命令完全取出容器.Pass -- volume还可以删除Redis容器使用的数据卷:
```shell
$ docker-compose down --volumes
```
在以上例子,您已经看到了Compose的工作原理.
