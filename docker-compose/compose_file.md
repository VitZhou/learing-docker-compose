# Compose File
Compose File是一个YAML文件,用于定义Docker应用程序的服务,网络和volume.
这些参考文献中描述了Compose file格式,具体针对每个版本.
以下主题解释了版本之间的差异,Docker Engine的兼容性以及如何升级.
## 兼容性矩阵
| Compose file format | Docker Engine release |
|--------|--------|
|    3.0 ; 3.1    |     1.13.0+   |
|	2.1		|		1.12.0+		|
|	2.0	|		1.10.0+		|
|	1.0	|		1.9.1.+		|

## Versioning(版本控制)
目前有三个版本的Compose文件格式:
- 版本1,旧版格式.这是通过省略YAML根目录下的vesion key来指定的.
- 版本2.x.这是在YAML的根目录下指定一个version：'2'或version：'2.1'条目.
- 版本3.x,最新和推荐的版本,旨在在Compose和Docker Engine的群组模式之间进行交叉兼容.这是使用version：'3'或version：'3.1'等指定的,在YAML的根目录下.

> 其他内容都是版本特性内容,暂时对我没多大帮助以后再译