# 写dockerfiles的最佳实践

你可以参考[dockerfile的详细手册页](https://docs.docker.com/engine/reference/builder/)

也可以参考别人的[dockfile](https://github.com/docker-library/buildpack-deps/blob/master/jessie/Dockerfile)

## 一般指南和建议

### 容器应该是短暂的

我们的意思是容器应该可以停止和销毁，并且可以使用最低的设置和配置重新启动一个新容器

### 使用 .dockerignore文件

.dockerignore和gitignore文件的作用差不多

是用来忽略文件的

[.dockerignore](https://docs.docker.com/engine/reference/builder/#dockerignore-file)

### 避免安装不必要的包

为了减少复杂性、依赖项、文件大小和创建时间，您应该避免安装额外的或不必要的包。例如，您不需要在数据库映像中包含文本编辑器。

### 一个容器只运行一个进程

在几乎所有情况下，只能在单个容器中运行单个进程.。将应用程序解耦到多个容器中，可以更方便地水平缩放和重用容器.。如果该服务依赖于另一个服务，则使用[容器链接.](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/)。

### 尽量减少层数

尽量减少dockerfile层数的使用。

### 排序多行参数

通过一个反斜杠\(\\)可以将参数写在不同的行，也方便增加或删减包，以及其他操作

像下面这样

RUN apt-get update && apt-get install -y \

  bzr \

  cvs \

  git \

  mercurial \

  subversion

### 构建过程中的缓存

镜像创建过程中会使用缓存，例如已经存在的镜像，和使用过的文件，使用缓存有助于减少镜像的大小

如果你不想使用缓存，使用--no-cache=true选项就可以。



## Dockerfile 指令介绍

以下提供了一些建议，它会帮助我们编写更好的Dockerfile。

 FROM

尽可能使用官方仓库作为基础镜像。推荐使用Debian image因为它是严格控制的并且占用存储比较小\(150MB左右\)。

[详细参考](https://docs.docker.com/engine/reference/builder/#from)

**标签**

[理解标签](https://docs.docker.com/engine/userguide/labels-custom-metadata/)

您可以添加标签到您的镜像，以帮助组织镜像的项目，记录许可信息，以帮助自动化，或出于其他原因。每个标签需要添加一个以标签开头的行，并带有一个或多个键值对.。下面的示例显示了不同的可接受格式。

    注意空格，"符号需要转义

```
#设置一个或多个单独的标签
LABEL com.example.version="0.0.1-beta"
LABEL vendor="ACME Incorporated"
LABEL com.example.release-date="2015-02-12"
LABEL com.example.version.is-production=""

# 在一行设置多个标签
LABEL com.example.version="0.0.1-beta" com.example.release-date="2015-02-12"

# 一次设置多个标签
LABEL vendor=ACME\ Incorporated \
      com.example.is-beta= \
      com.example.is-production="" \
      com.example.version="0.0.1-beta" \
      com.example.release-date="2015-02-12"

```

[管理标签参考](https://docs.docker.com/engine/userguide/labels-custom-metadata/#managing-labels-on-objects)

 RUN

为了使得Dockerfile更可读、更容易理解和维护，尽量将单行长的复杂的RUN语句拆分为用反斜线\( \ \)分隔的多行。

[run 参考](https://docs.docker.com/engine/reference/builder/#run)

 apt-get

RUN apt-get经常用来安装包，但他有一些陷阱需要小心。

 我们要避免运行RUN apt-get upgrade或者dist-upgrade命令，因为很多基础镜像“必要”的包不应该在容器中更新。如果一个包过期了，我们需要联系它的维护者。如果我 们知道有一个特殊包foo需要更新，使用apt-get install -y foo来自动更新即可。



 我们经常结合update和install 在同一个RUN语句中，如下:

RUN apt-get update && apt-get install -y \

package-bar  \

package-baz \

package-foo



单独使用apt-get update会引起缓存问题，并且随后的apt-get install指令会失败。例如如下的Dockerfile内容:

```
 FROM ubuntu:14.04


 RUN apt-get update


 RUN apt-get install -y curl
```

创建镜像后，所有层都在Docker的缓存中。假如你后来修改了apt-get install通过添加额外包 :

```
  FROM ubuntu:14.04


  RUN apt-get update


  RUN apt-get install -y curl nginx
```

结果RUN apt-get update不会被执行，我们的创建结果可能会获得一个过期版本的curl和nginx包。

使用如下命令就可以确保每次安装的都是最新版本的包。这个技术以cache busting\(缓存失效\)著称。我们也可以通过指定包版本来达到同样的效果。这种方法命名为穿钉版本。如下示例:

```
 RUN apt-get update && apt-get install -y \


 package-bar \


 package-baz \


 package-foo=1.3.*
```

穿钉版本技术强迫创建镜像获取指定版本，这额技术可以减少因依赖包的意外变更导致的失败率。

以下是一个建议使用的RUN命令示例:

```
 RUN apt-get update && apt-get install -y \
aufs-tools \
automake \
build-essential \
curl \
dpkg-sig \
libcap-dev \
libsqlite3-dev \
mercurial \
reprepro \
ruby1.9.1 \
ruby1.9.1-dev \
s3cmd=1.1.* \
&& rm -rf /var/lib/apt/lists/*
```

s3cmd命令指定为1.1.\* 版本。如果以前镜像使用的是一个老版本，这就会导致一个缓存无效，执行apt-get update命令然后安装这个新版本。

 另外，清理apt缓存并且删除/var/lib/apt/lists可以减小镜像的大小。由于RUN语句开始于apt-get update，包缓存将总是之前的apt-get install被刷新。



 说明: 官方的Debian和Ubuntu镜像自动运行apt-get clean,所以没有必要明确调用。

CMD

[CMD参考](https://docs.docker.com/engine/reference/builder/#cmd)

CMD 指令应该用于运行镜像中包含的软件，同时可以携带任意的参数。CMD应该总是使用exec格式\(CMD\["executable", "param1", "param2"...\]\).因此如果镜像是用来提供一个服务\(Apache,Rails,etc\)，我们应该像这样执行CMD\["apache2", "-DFOREGROUND"\]。实际上，推荐任何提供服务的基础镜像都使用这种指令的格式。

在大多数情 况下，CMD应该提供一个交互的shell\(bash,python,perl,etc\)，例如,CMD\["perl","- de0"\]，CMD\["python"\],或者CMD\["php", "-a"\].CMD应该少用于CMD\["param","param"\]的方式与ENTRYPOINT联合使用,除非我们对ENTRYPOINT的工作原 理非常了解了。

 EXPOSE

 [EXPOSE参考](https://docs.docker.com/engine/reference/builder/#expose)

EXPOSE指令指示容器将要监听连接的端口。所以我们应该使用通用的、传统的端口给我们的应用。例如包含Apache的web服务的镜像将使用EXPOSE 80，一个包含MongoDB服务的镜像使用EXPOSE 27017等等。

 ENV

[ENV参考](https://docs.docker.com/engine/reference/builder/#env)

为了使得新软件更容易运行，我们常使用ENV更新PATH环境变量，例如ENV PATH /usr/local/nginx/bin:$PATH将要确保CMD\["nginx"\]可以工作。

ADD or COPY

[ADD参考](https://docs.docker.com/engine/reference/builder/#add)

[COPY参考](https://docs.docker.com/engine/reference/builder/#copy)

ADD 和COPY功能相似，COPY是首选。因为它比ADD更加透明.COPY仅支持基础的本地文件拷贝到容器中，ADD有些特性\(本地tar包解压和远程 URL支持\) 不会立即显示。ADD最好的应用是本地tar包自动解压到镜像中，如ADD rootfs.tar.xz /。

如果我们在Dockerfile中有多个步骤使用不同文件，逐个COPY这些文件，而不是拷贝所有文件。这样确保每步骤的缓存会还重新更新。

```
COPY requirements.txt /tmp/


 RUN pip install --requirement /tmp/requirements.txt


 COPY . /tmp/
```

出于镜像大小考虑，强烈不建议使用ADD获取远程URL地址的包，我们应该使用curl或者wget替代。这样我们可以在解压后删除不用的文件，而不必添加到镜像的另一层。我们避免使用如下形式:

```
ADD http://example.com/big.tar.xz /usr/src/things/
RUN tar -xJf /usr/src/things/big.tar.xz -C /usr/src/things
RUN make -C /usr/src/things all
```

应该替换成如下形式:

```
RUN mkdir -p /usr/src/things \
    && curl -SL http://example.com/big.tar.xz \
    | tar -xJC /usr/src/things \
    && make -C /usr/src/things all
```

ENTRYPOINT

[ENTRYPOINT参考](https://docs.docker.com/engine/reference/builder/#entrypoint)

ENTRYPOINT最好用于设置镜像的主要命令，这个设置允许镜像像一个命令一样运行。

我们来看下这个例子:

```
ENTRYPOINT ["s3cmd"]
CMD ["--help"]
```

使用这个镜像运行下面的命令:

```
$ docker run s3cmd
```

或者使用右面的参数来执行一个命令:

```
$ docker run s3cmd ls s3://mybucket
```

注意到镜像的名字又可以作为一个它包含的可执行程序的参考，这对于我们理解镜像作用是有帮助的。

ENTRYPOINT指令可以用来与帮助脚本结合使用。如下示例:

这个镜像的链接：[Postgres Official Image](https://hub.docker.com/_/postgres/)

```
 #!/bin/bash
set -e

if [ "$1" = 'postgres' ]; then
    chown -R postgres "$PGDATA"

    if [ -z "$(ls -A "$PGDATA")" ]; then
        gosu postgres initdb
    fi

    exec gosu postgres "$@"
fi

exec "$@"
```

说明: 脚本使用了[exec Bash命令](http://wiki.bash-hackers.org/commands/builtin/exec)，因此运行脚本后的应用进程在容器中的PID为1,这样可以保证进程接收到任何发送给容器的UNIX信号。

这个帮助脚本会被拷贝到容器中，然后通过ENTRYPOINT指令在容器中执行。

```
COPY ./docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]
```

脚本允许用户使用多种方式进行交互。下面是一种简单的方式:

|  $ docker run postgres |
| :--- |


也可以在运行时通过传入参数给服务:

|  $ docker run postgres postgres --help |
| :--- |


最后也可以通过交互的Bash、Perl、python等工具来执行:

|  $ docker run --rm -it postgres bash |
| :--- |


VOLUME

[VOLUME参考](https://docs.docker.com/engine/reference/builder/#volume)

VOLUME指令应该用于如下内容:任何类型的数据库存储区域、配置存储、容器创建的文件或目录。

 推荐VOLUME用于挂载镜像中那些经常变化\(易变化的\)或者用户可维护的部分。



USER

[USER参考](https://docs.docker.com/engine/reference/builder/#user)

一个服务如果不需要超级权限，建议使用USER切换成非root用户执行。在Dockerfile中创建用户和组的方式如下:

| RUN groupadd -r postgres && useradd -r -g postgres postgres |
| :--- |


 说明: 重建镜像时如果不明确指定UID/GID，我们每次可能得到的UID/GID都不会一样，因此我们应该明确的指定UID/GID。

我们尽量避免安装和使用sudo，因为它有不可预知的TTY和信号转发行为，这会给我们带来更多问题需要解决。我们可以使用[gosu](https://github.com/tianon/gosu)\(这个项目可以在github上找到\)替代它。

最后，为了减少层数和复杂度，避免频繁使用USER进行用户切换。

WORKDIR

[WORKDIR参考](https://docs.docker.com/engine/reference/builder/#workdir)

为了清晰度和可靠性，我们应该确保WORKDIR在镜像中使用绝对路径。我们应该使用WORKDIR而不是使用类似这些指令RUN cd ... && do-something ，这会影响其可读性、问题定位和可维护性。

ONBUILD

[ONBUILD参考](https://docs.docker.com/engine/reference/builder/#onbuild)

ONBUILD指令在执行Dockerfile创建新镜像后会存储到镜像的manifest清单中，我们可以通过docker inspect查看OnBuild的信息。

当 我们使用带有ONBUILD触发器的镜像作为基础镜像来创建新镜像时，当Dockerfile执行到FROM时会自动查找OnBuild信息并执行这个触 发器命令。成功后继续向下执行下一条指令，失败的话就停止向下执行并中止创建过程。如果成功创建了新的镜像后，这个新镜像中不会继承基础镜像中的 OOnBuild触发器内容。参考 Ruby’s ONBUILD variants

从ONBUILD创建的镜像应该单独做一个标记，如ruby:1.9-onbuild 或者ruby:2.0-onbuild。

当把ADD或者COPY加入ONBUILD中时要小心，如果新创建镜像的上下文缺少这些要添加的资源情况会导致创建的失败。

## 官方示范仓库

你可以看一下这些示范仓库的Dockerfiles

[Go](https://hub.docker.com/_/golang/)

[Perl](https://hub.docker.com/_/perl/)

[Hy](https://hub.docker.com/_/hylang/)

[Rails](https://hub.docker.com/_/rails)

## 另外的一些资源

[Dockerfile参考](https://docs.docker.com/engine/reference/builder/)

[关于基本镜像\(从零开始\)](https://docs.docker.com/engine/userguide/eng-image/baseimages/)

[关于自动构建镜像](https://docs.docker.com/docker-hub/builds/)

[创建你的官方仓库](https://docs.docker.com/docker-hub/official_repos/)



