# 创建一个基本镜像

你想创建自己的基本图像？

具体过程将在很大程度上取决于您想要封装的Linux发行版。我们下面有一些例子。

## 创建一个完整的镜像通过tar包

例如使用debootstrap创建ubuntu基本镜像

```
$ sudo debootstrap raring raring > /dev/null
$ sudo tar -C raring -c . | docker import - raring

a29c15f1bf7a

$ docker run raring cat /etc/lsb-release

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=13.04
DISTRIB_CODENAME=raring
DISTRIB_DESCRIPTION="Ubuntu 13.04"

```

你可以查看更多示例

[BusyBox](https://github.com/docker/docker/blob/master/contrib/mkimage-busybox.sh)

CentOS / Scientific Linux CERN \(SLC\) [on Debian/Ubuntu](https://github.com/docker/docker/blob/master/contrib/mkimage-rinse.sh)or [on CentOS/RHEL/SLC/etc.](https://github.com/docker/docker/blob/master/contrib/mkimage-yum.sh)

[Debian / Ubuntu](https://github.com/docker/docker/blob/master/contrib/mkimage-debootstrap.sh)

## 创建基本镜像，通过Dockerfiles的FROM scrath

你可以创建最小镜像，像下面这样

假设hello文件是可执行文件，你写好了代码在里面

```
FROM scratch
ADD hello /
CMD ["/hello"]
```

[这里有一个可执行文件的范本](https://github.com/docker-library/hello-world/blob/master/hello.c)

注意因为windows和mac电脑使用了linux的虚拟机，所以需要通过linux编译可执行文件，不用担心你可以快速下载他

```
$ docker run --rm -it -v $PWD:/build ubuntu:16:04
container# apt-get install build-essential
container# cd /build
container# gcc -o hello -static hello.c
```

现在你可以在windows或Mac上运行docker run hello了

[这个镜像的文件托管在github](https://github.com/docker-library/hello-world)

## 更多资源

[Dockerfile参考](https://docs.docker.com/engine/reference/builder/)

[关于Dockerfile的最佳实践](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)

[创建你的镜像仓库，通过docker hub](https://docs.docker.com/docker-hub/official_repos/)



