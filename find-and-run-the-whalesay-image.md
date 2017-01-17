人们可以在世界各地创建[Docker](http://lib.csdn.net/base/docker)镜像, 你可以通过浏览Docker Hub找到这些镜像. 在接下来的部分, 你会搜索并找到将在这个入门教程中会使用到的镜像。

### 第一步, 定位whalesay镜像 {#第一步-定位whalesay镜像}

1. 打开浏览器, 访问[Docker Hub](https://hub.docker.com/)

![](/assets/browse_and_search.png)

Docker Hub包含的镜像有来自个人的, 也有来自RedHat, IBM, Google等等官方机构的

点击”搜索”

浏览器打开搜索页面

搜索框中输入关键字”whalesay”

![](/assets/image_found.png)点击docker/whalesay进入，里面是介绍说明，详细的告诉你这是个什么样的程序，通过什么命令来下载运行。



![](/assets/whale_repo.png)每个镜像库包含了镜像的信息. 这些信息包括镜像中包含了那些类型的软件, 和如何使用的信息. 你可能注意到了, whalesay镜像是基于

linux发行版的Ubuntu. 在下一阶段, 你将运行whalesay镜像在你计算机上.

第二步, 运行whalesay镜像

$ docker run docker/whalesay cowsay boo

你第一次运行镜像时, Docker命令会在本地查找是否存在这个镜像. 如果镜像不存在, Docker会从Docker Hub中下载这个镜像

当你运行一个镜像在容器中时, Docker下载这个镜像到你的计算机中, 这个本地的镜像复制会节省你到时间. Docker只会在镜像在Docker Hub上发生变化时才会再次下载. 当然你也可以删除这个镜像. 后面你会了解更多. 现在让我们离开镜像, 因为我们稍后要再次使用它.

使用docker images 命令可以查看自己已经下载的镜像

花一些时间操作一下whalesay容器



试着用一个词或短语再次运行whalesay镜像。尝试长或短的短语.

$ docker run docker/whalesay cowsay boo-boo











