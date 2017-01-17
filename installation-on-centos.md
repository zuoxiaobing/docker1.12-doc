在CentOS的安装docker

预计阅读时间：6分钟

在centos7上安装docker。其他兼容el7分区的系统Linux可能安装成功，但Docker没有测试过。

这些安装包由docker维护，以确保您获得最新版本的docker。如果你希望使用CentOS的rpm安装，查询以下rpm之类的文档吧。

前提

docker要求有64位版本的OS和3.10的Linux内核 。

使用以下命令检查你的内核版本：

$ uname -r

3.10.0-229.el7.x86 \_ 64

最后，推荐你完全更新你的系统。以及修复内核的bug

安装Docker Engine

有两种安装方式Docker引擎。你可以使用yum安装。或者你可以用curl的get.docker.com网站。第二种方法通过运行安装脚本，也是通过yum安装。

安装yum

1. 用root登录到你的机器，或者使用sudo命令。

   2.确保您现有的软件包是最新的。







