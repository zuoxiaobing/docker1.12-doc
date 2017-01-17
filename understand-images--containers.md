了解images\(镜像\)和容器\(containers\)

预计阅读时间：1分钟

docker Engine提供核心技术运行images成为一个容器。在你安装的最后一步，运行的docker run Hello-World命令。你跑的命令有三个部分。

![](/assets/container_explainer.png)

容器的解释

images是一个文件系统和运行时使用的参数.。它没有状态，也不会改变。容器是镜像的运行实例.。当你运行这个命令时，Docker Engine所做的是：

 检查本地是否存在这个镜像

如果没有则下载

运行这个镜像成为一个容器

docker run Hello-World这个命令所运行的镜像仅仅运行了一个简单命令，但你可以做更多事，例如创建数据库之类的

你可以创建一个镜像并且在任何装了docker Engine的机器上运行它



