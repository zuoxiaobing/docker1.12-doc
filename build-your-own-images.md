你可以在运行时指定版本，像这样

$ docker run -t -i ubuntu:14.04 /bin/bash

其中ubuntu:14.04不仅指定了镜像还指定了版本，如果不指定版本会使用最新版

## 更新你的镜像通过commit命令

首先进入镜像，装一个软件

$ docker run -t -i training/sinatra /bin/bash

root@0b2616b0e5a8:/\#

root@0b2616b0e5a8:/\# apt-get install -y ruby2.0-dev ruby2.0

root@0b2616b0e5a8:/\# gem2.0 install json

然后退出容器使用exit命令

执行commit命令更新容器

$ docker commit -m "Added json gem" -a "Kate Smith"  0b2616b0e5a8 ouruser/sinatra:v3

其中

-m标记用于显示提示信息

-a标记用于指定作者

ouruser/sinatra:v3用于指定更新后镜像及版本名

## 创建一个新的镜像通过Dockerfile文件

我们建议使用Dockerfile文件更新镜像，而不是commit命令，因为Dockerfile文件更易于分享

$ mkdir sinatra



$ cd sinatra



$ touch Dockerfile

$ vi Dockerfile

\# This is a comment

FROM ubuntu:14.04

MAINTAINER Kate Smith &lt;ksmith@example.com&gt;

RUN apt-get update && apt-get install -y ruby ruby-dev

RUN gem install sinatra

然后保存退出Dockerfile文件

\#号后的内容是注释

现在可以创建镜像了，不要遗忘命令后面的** . **符号

$ docker build -t ouruser/sinatra:v2 .

移除镜像

$ docker rmi training/sinatra

检查镜像或容器的大小

检查镜像大小

$ docker history centos:centos7

检查容器大小

$ docker ps -s





