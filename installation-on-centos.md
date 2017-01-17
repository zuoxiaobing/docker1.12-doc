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

1.用root登录到你的机器，或者使用sudo命令。

2.确保您现有的软件包是最新的

$ sudo yum update

3.添加 yum repo

$ sudo tee /etc/yum.repos.d/docker.repo &lt;&lt;-'EOF'

\[dockerrepo\]

name=Docker Repository

baseurl=https://yum.dockerproject.org/repo/main/centos/7/

enabled=1

gpgcheck=1

gpgkey=https://yum.dockerproject.org/gpg

EOF

4.安装 Docker .

$ sudo yum install docker-engine

5.开机启动Docker

$ sudo systemctl enable docker.service

6.启动docker服务

$ sudo systemctl start docker

7.验证docker启动是否成功，运行测试镜像为一个容器



 $ sudo docker run --rm hello-world



 Unable to find image 'hello-world:latest' locally

 latest: Pulling from library/hello-world

 c04b14da8d14: Pull complete

 Digest: sha256:0256e8a36e2070f7bf2d0b0763dbabdd67798512411de4cdcf9431a1feb60fd9

 Status: Downloaded newer image for hello-world:latest



 Hello from Docker!

 This message shows that your installation appears to be working correctly.



 To generate this message, Docker took the following steps:

  1. The Docker client contacted the Docker daemon.

  2. The Docker daemon pulled the "hello-world" image from the Docker Hub.

  3. The Docker daemon created a new container from that image which runs the

     executable that produces the output you are currently reading.

  4. The Docker daemon streamed that output to the Docker client, which sent it

     to your terminal.



 To try something more ambitious, you can run an Ubuntu container with:

  $ docker run -it ubuntu bash



 Share images, automate workflows, and more with a free Docker Hub account:

  https://hub.docker.com



 For more examples and ideas, visit:

  https://docs.docker.com/engine/userguide/



如果你需要添加一个HTTP代理，为Docker运行时文件设置不同的目录或分区，或进行其他自定义，阅读我们的关于系统的文章来学习如何[定制你的系统Docker守护进程选项。](https://docs.docker.com/engine/admin/systemd/)



通过脚本安装

1.用root登录到你的机器，或者使用sudo命令。

2.确保您现有的软件包是最新的

$ sudo yum update

运行 Docker安装脚本 

$ curl -fsSL https://get.docker.com/ \| sh

这个脚本添加 docker.repo 仓库 并且 安装 Docker.

3.开机启动Docker

$ sudo systemctl enable docker.service

4.启动docker服务

$ sudo systemctl start docker

5.验证docker启动是否成功，运行测试镜像为一个容器



 $ sudo docker run --rm hello-world

如果你需要添加一个HTTP代理，为Docker运行时文件设置不同的目录或分区，或进行其他自定义，阅读我们的关于系统的文章来学习如何[定制你的系统Docker守护进程选项。](https://docs.docker.com/engine/admin/systemd/)

创建一个docker用户组

因为一些原因，Docker服务始终运行在root用户上。

为了避免使用sudo命令，创建一个docker用户组。当docker启动后，运行容器的所有者变成docker用户组。

警告：docker用户组相当于root用户；[如何影响你的系统的安全细节](https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface)



创建docker用户组和添加用户到docker用户组：

1.用root登录到你的机器，或者使用sudo命令。

创建docker用户组。

$ sudo groupadd docker

添加用户到docker用户组

$ sudo usermod -aG docker your\_username

退出并且用新添加的用户登陆

验证

$ docker  run  --rm hello-world



卸载

你可以通过yum卸载

1.列出安装包

 $ yum list installed \| grep docker



    docker-engine.x86\_64                   1.12.3-1.el7.centos             @dockerrepo

    docker-engine-selinux.noarch           1.12.3-1.el7.centos             @dockerrepo    $ sudo yum -y remove docker-engine.x86\_64

2.删除安装包 

$ sudo yum -y remove docker-engine.x86\_64

   $ sudo yum -y remove docker-engine-selinux.noarch

3.删除一些文件

$ rm -rf /var/lib/docker

4.删除配置文件以及你创建的用户















