whalesay镜像可以做到的更好, 尤其是当你不知道自己想要说什么的时候. 你可以输入更多的命令来让它发声.

[`Docker`](http://lib.csdn.net/base/docker)` run docker/whalesay cowsay boo-boo`

在接下来的部分, 你会改进并构建一个新的版本, 这个版本只需要非常少的命令就可以让他”自说自话”.

# 第一步: 写Dockerfile {#第一步-写dockerfile}

1.创建一个目录

$mkdir `mydockerbuild`

这个目录就相当于一个你构建是的”环境”. 这个”环境”意味着构建镜像时你包含的所有东西

2.进入到你新建的文件夹

$ `cd mydockerbuild`

当前的这个文件夹是空的.

通过命令

$`touch Dockerfile`

在这个文件夹下创建一个Dockerfile文件

用文件编辑器打开Dockerfile文件

$ vi Dockerfile

在打开的文件中输入

`FROM docker/whalesay:latest`

关键字FROM告诉Docker你的镜像是基于哪个镜像构建的. 你是基于已经存在的whalesay镜像创建新的工作

现在, 添加fortunes程序到镜像

$RUN apt-get -y update && apt-get install -y fortunes

fortunes程序是一个让whale聪明的说出语句的命令. 所以, 我们需要先安装这个程序. 这一行使用了

`apt-get`命令安装fortunes, 如果这些对你来说很难理解, 不用担心, 只要你输入正确, 他们就会正常运行的.

一旦镜像有了它所需的软件, 就可以在命令他在加载镜像的时候运行软件.

$CMD /usr/games/fortune -a \| cowsay

这一行是告诉fortune程序发送一个俏皮的引用给cowsay程序

检查你的Dockerfile文件，它应该像这样

FROM docker/whalesay:latest

RUN apt-get -y update && apt-get install -y fortunes

CMD /usr/games/fortune -a \| cowsay

保存你的文件

# 第二步: 根据Dockerfile构建你的镜像 {#第二步-根据dockerfile构建你的镜像}

现在, 在终端中输入

$`docker build -t docker-whale .`

构建你的新镜像\(不要遗漏 . \)

该命令需要几秒钟运行并报告其结果。在你使用新的镜像之前，花一点时间了解Dockerfile构建过程。

命令`docker build -t docker-whale .`会在本机使用当前目录下的Dockerfile构建一个叫docker-whale的镜像. 这个命令会运行大概一分钟的时间, 输出的内容看起来很长而且很复杂. 在这一部分, 你会学习到这些输出所表示的内容.

首先Docker会检查所有需要构建的内容.

`Sending build context to Docker daemon 158.8 MB`

然后, Docker加载whalesay镜像. 它是一个已经存在的, 不需要下载的镜像.

```
Step0: FROMdocker/whalesay:latest
 -->fb434121fc77
```

Docker进行下一步更新apt-get包管理器. 会输出很多, 没必要在一一列举



然后, Docker安装新的fortunes软件



最后, Docker完成构建并输入结果

# 第四步: 运行新的镜像docker-whale {#第四步-运行新的镜像docker-whale}

输入`docker images`这个命令，你可能还记得，列出了在本地的图像。

在命令行运行新镜像

$`docker run docker-whale`

正如你所看到的，你所做的whale聪明很多。他可能会输出一些名人名言。每次运行输出的结果都会不同。

#  {#第四步-运行新的镜像docker-whale}



