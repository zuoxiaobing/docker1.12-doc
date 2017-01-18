# hello world

学习docker run这个命令

运行一个容器

$ docker run ubuntu /bin/echo 'Hello world'

Hello world

docker run  这部分 用于运行一个容器

ubuntu 是一个镜像，如果本地没有将下载

/bin/echo 是一个命令用于运行在容器里面

‘hello world‘是命令的一部分，他会打印出hello world

# 运行一个交互式容器

 $ docker run -t -i ubuntu /bin/bash

 root@af8bae53bdd3:/\#

在这个示例里面

docker run  这部分 用于运行一个容器

ubuntu 是一个镜像，如果本地没有将下载

-t 标记用于打开命令行终端

-i 标记用于允许交互

/bin/bash用于指定shell语言

现在你可以在容器里运行你的操作，结束后输入命令exit或按ctrl+d退出容器

注意:当你退出容器时，容器就销毁了

# 开始运行一个后台容器

 $ docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147

docker run  这部分 用于运行一个容器

-d 标记用于指定在后台运行

ubuntu 是一个镜像，如果本地没有将下载

1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147这个长字符串被称为容器ID。它唯一标识一个容器，以便我们可以使用它。

docker ps命令可以列出正在运行的容器

$ docker ps

CONTAINER ID  IMAGE         COMMAND               CREATED        STATUS       PORTS NAMES

1e5535038e28  ubuntu  /bin/sh -c 'while tr  2 minutes ago  Up 1 minute        insane\_babbage

其中 insane\_babbage是容器名称，如果没有指定的话，他是随机的

docker logs 容器名可以获得容器里面正在输出的内容

$ docker logs insane\_babbage

hello world

hello world

hello world

docker stop - 可以停止一个正在运行的容器

$docker stop insane\_babbage



