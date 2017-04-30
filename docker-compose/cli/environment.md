# Docker Compose CLI 环境变量
几个环境变量可用于配置Docker Compose命令行行为.
以DOCKER_开头的变量与用于配置Docker命令行客户端的变量相同.如果使用docker-machine,那么eval"$(docker-machine env my-docker-vm)"命令应该将它们设置为正确的值.(在本示例中,my-docker-vm是您创建的计算机的名称.)
> 这些变量中的某些变量也可以使用[环境文件](https://docs.docker.com/compose/env-file/)来提供

## COMPOSE_PROJECT_NAME
设置项目名称.此值与启动时的容器的服务名称一起添加.例如,如果您的项目名称是myapp,并且它包含两个服务db和web,则分别创建名为myapp_db_1和myapp_web_1的容器.

设置这是可选的.如果没有设置,COMPOSE_PROJECT_NAME默认为项目目录的基础名称.另请参见-p命令行选项.

## COMPOSE_FILE
指定Compose文件的路径.如果没有提供,Compose会在当前目录中找到名为docker-compose.yml的文件,然后连续地查找每个父目录,直到找到该名称的文件.
此变量支持由路径分隔符分隔的多个compose file(在Linux和macOS上,路径分隔符为: ,在Windows上为; .).例如：COMPOSE_FILE = docker-compose.yml:docker-compose.prod.yml.路径分隔符也可以使用COMPOSE_PATH_SEPARATOR进行自定义.

## COMPOSE_API_VERSION
Docker API仅支持报告特定版本的客户端的请求.如果您收到客户端和服务器的Docker-compose不具有相同的版本错误,则可以通过设置此环境变量来解决此错误.设置版本值以匹配服务器版.

设置此变量旨在作为您需要临时运行客户端和服务器版本不匹配的情况的解决方法.例如,如果您可以升级客户端,但需要等待升级服务器.

运行此变量集和已知的不匹配会阻止某些Docker功能正常工作.失败的确切功能将取决于Docker客户端和服务器版本.因此,使用此变量集运行仅作为解决方法,并未正式支持.

如果您遇到运行此集的问题,请通过升级解决不匹配,并删除此设置,以查看您的问题是否在通知支持之前解决.

## DOCKER_HOST
设置docker daemon的URL.与Docker客户端一样,默认为unix：///var/run/docker.sock.

## DOCKER_TLS_VERIFY
当设置为空字符串以外的任何内容时,可以启用与docker daemon的TLS通信.

## DOCKER_CERT_PATH
配置用于TLS验证的ca.pem,cert.pem和key.pem文件的路径.默认为〜/.docker.

## COMPOSE_HTTP_TIMEOUT
配置在Compose认为失败之前允许Docker Daemon挂起的时间(以秒为单位).默认为60秒.

## COMPOSE_TLS_VERSION
配置哪个TLS版本用于与docker daemon的TLS通信.默认为TLSv1.支持的值有:TLSv1,TLSv1_1,TLSv1_2.

## COMPOSE_CONVERT_WINDOWS_PATHS
在挂载卷定义中启用从Windows风格到Unix风格的路径转换.Docker Machine和Docker Toolbox在Windows上的用户应该始终设置此选项.默认为0.支持的值：true或1启用,false或0禁用.

## COMPOSE_PATH_SEPARATOR
如果设置,COMPOSE_FILE环境变量的值将使用此字符作为路径分隔符分隔.