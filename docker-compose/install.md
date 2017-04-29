# 安装docker Compose
您可以在macOS,Windows和64位Linux上运行Compose.想要安装它,您需要先安装Docker.
要安装Compose,请执行以下操作：
1. 安装docker
	- [Mac](https://docs.docker.com/docker-for-mac/)
	- [Windows](https://docs.docker.com/docker-for-windows/)
	- [Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/)
	- [其他](https://docs.docker.com/engine/installation/)

1. Mac版本Docker,Windows版本Docker和Docker Toolbox都包含Docker Compose,因此大多数Mac和Windows用户不需要单独安装Docker Compose.如果您直接在Microsoft Windows Server 2016上运行Docker daemon和客户端（使用Docker EE for Windows Server 2016）,则需要安装Docker Compose.运行以下命令下载Docker Compose,将$ dockerComposeVersion替换为要使用的特定版本的Compose：
	```shell
    	Invoke-WebRequest "https://github.com/docker/compose/releases/download/$dockerComposeVersion/docker-compose-Windows-x86_64.exe" -UseBasicParsing -OutFile $Env:ProgramFiles\docker\docker-compose.exe
    ```

1. 在Linux上,您可以从GitHub上的[Compose存储库版本页面](https://github.com/docker/compose/releases)下载Docker Compose二进制文件. 按照链接中的说明进行操作,包括在终端中运行curl命令来下载二进制文件.
> 注意：如果您收到“Permission denied”错误,您的/usr/local/bin目录可能不可写,您将需要作为超级用户安装Compose.运行sudo -i,然后下面的两个命令,然后退出.

    ```shell
    curl -L https://github.com/docker/compose/releases/download/1.12.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    ```

1. 修改权限
```shell
sudo chmod +x /usr/local/bin/docker-compose
```

1. (可选)用bash或者zsh shell命令完成安装

1. 测试是否安装成功
```bash
$ docker-compose --version
docker-compose version 1.12.0, build b31ff33
```

## 安装替代选项
### 使用pip安装
可以使用pip从pypi安装Compose.如果使用pip安装,我们建议您使用virtualenv,因为许多操作系统都具有与docker-compose依赖关系相冲突的python系统软件包.请参阅[virtualenv教程](http://docs.python-guide.org/en/latest/dev/virtualenvs/)开始使用.
```shell
pip install docker-compose
```
如果你不使用virtualenv:
```shell
sudo pip install docker-compose
```
>注意：需要pip 6.0或更高版本.

### 作为容器安装
Compose也可以从容器中运行,一个小的bash脚本包装器.要将compose作为容器运行：
```shell
$ curl -L --fail https://github.com/docker/compose/releases/download/1.12.0/run.sh > /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```

## 升级
如果要从Compose 1.2或更早版本进行升级,则需要在升级Compose之后删除或迁移现有容器.这是因为,从版本1.3起,Compose使用Docker标签来跟踪容器,因此需要重新创建标签.

如果Compose检测到没有标签创建的容器,它将拒绝运行,这样您就不会得到两套Compose.如果要继续使用现有容器（例如,因为它们具有要保留的数据卷）,则可以使用compose 1.5.x使用以下命令进行迁移：
```shell
docker-compose migrate-to-labels
```
或者.如果您不担心保留它们,您可以删除它们.Compose只会创建新的.
```shell
docker rm -f -v myapp_web_1 myapp_db_1 ...
```

## 卸载
如果您使用curl安装,卸载Docker Compose：
```shell
sudo rm /usr/local/bin/docker-compose
```
如果使用pip安装:
```shell
pip uninstall docker-compose
```
> 如果您使用上述任一方法获得“Permission denied”错误,您可能没有适当的权限来删除docker-compose.要强制删除,请先将sudo添加到上述任一命令中并重新运行.