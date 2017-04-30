# Command-line Completion
Compose带有bash和zsh shell的Command Completion

## 安装
### Bash
确保安装了bash完成.如果您在非最小安装中使用当前的Linux,则应完成bash完成.在Mac上,安装brew install bash-completion
将完成脚本放在/etc/bash_completion.d/(在mac上是:/usr/local/etc/bash_completion.d/),使用例如:
```shell
curl -L https://raw.githubusercontent.com/docker/compose/$(docker-compose version --short)/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```
然后将以下内容添加到〜/.bash_profile
```shell
if [ -f $(brew --prefix)/etc/bash_completion ]; then
. $(brew --prefix)/etc/bash_completion
fi
```
您可以输入您的〜/.bash_profile或启动新的终端以利用completion.

### MacPorts Bash Completion
如果您使用MacPorts,您需要稍微修改您的步骤到以下运行sudo端口安装bash-completion来安装bash completion
```shell
if [ -f /opt/local/etc/profile.d/bash_completion.sh ]; then
    . /opt/local/etc/profile.d/bash_completion.sh
fi
```
您可以输入您的〜/.bash_profile或启动新的终端以利用completion.

### Zsh
将completion脚本放在/path/to/zsh/completion中,使用例如〜/.zsh/completion/
```shell
$ mkdir -p ~/.zsh/completion
$ curl -L https://raw.githubusercontent.com/docker/compose/$(docker-compose version --short)/contrib/completion/zsh/_docker-compose > ~/.zsh/completion/_docker-compose
```
在$ fpath中包含目录,例如 通过添加〜/.zshrc
```shell
fpath=(~/.zsh/completion $fpath)
```
确保compinit已经加载,或者在〜/.zshrc中加入
```shell
autoload -Uz compinit && compinit -i
```
然后重新加载你的shell
```shell
exec $SHELL -l
```

### 可用的completion
到目前为止,根据您在命令行中键入的内容,将会完成
- 可用的docker-compose命令
- 可用于特定命令的选项
- 在给定上下文中有意义的服务名称(例如,基于镜像的运行或停止实例或服务的服务与基于Dockerfiles的服务).对于docker-compose scale,完成的服务名称将自动添加“=”.
- 所选选项的参数,例如docker-compose kill -s将完成一些信号,如SIGHUP和SIGUSR1.