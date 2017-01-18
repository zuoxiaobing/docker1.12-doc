学习更多关于docker 客户端的知识

$ docker version

这个命令用于查看docker版本以及一些相关信息

获得关于docker命令的一些帮助

例如

$ docker --help

$ docker attach --help

运行一个web应用程序

$ docker run -d -P training/webapp python app.py

-d 标记用于指定后台运行

-P 标记用于将容器中的任何所需网络端口映射到主机。让您可以查看Web应用程序。

training/webapp 是一个镜像

python app.py 是所运行的程序

docker ps -l 用于显示最后运行程序的详细信息

$ docker ps -l

CONTAINER ID  IMAGE                                 COMMAND        CREATED        STATUS                  PORTS                                         NAMES

bc533791f3f5  training/webapp:latest  python app.py  5 seconds ago  Up 2 seconds  0.0.0.0:49155-&gt;5000/tcp     nostalgic\_morse



PORTS

0.0.0.0:49155-&gt;5000/tcp

这一列用于显示主机映射的容器的端口

当然你也可以自己指定端口

$ docker run -d -p 80:5000 training/webapp python app.py

现在你可以尝试在浏览器中访问了通过ip地址

 $ docker port nostalgic\_morse 5000

这个命令用于显示哪个主机端口映射内内部端口5000

你还可以查看应用内部输出,通过容器名

$docker logs -f nostalgic\_morse

-f 标记 与 tail -f 标记的作用是一样的

## 观察容器的进程

$docker top nostalgic\_morse

## 检查这个web应用容器

$ docker inspect nostalgic\_morse

你能看见类似这样的json输出

\[{

    "ID": "bc533791f3f500b280a9626688bc79e342e3ea0d528efe3a86a51ecb28ea20",

    "Created": "2014-05-26T05:52:40.808952951Z",

    "Path": "python",

    "Args": \[

       "app.py"

    \],

    "Config": {

       "Hostname": "bc533791f3f5",

       "Domainname": "",

       "User": "",

. . .

您也可以通过请求特定元素缩小您想要返回的信息，例如返回容器的IP地址，您可以：

 $ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nostalgic\_morse

172.17.0.5

## 停止容器

$ docker stop nostalgic\_morse

## 重启容器

在你停止容器后，可以再启动一个容器，有两个选择，启动一个新容器或重新启动刚刚被停止的容器

$ docker start nostalgic\_morse

你也可以使用restart命令来先停止一个容器再启动他来执行重启

## 删除容器

$ docker stop nostalgic\_morse

$ docker rm nostalgic\_morse

删除容器需要先停止后删除，也可以使用rm -f 强制删除





