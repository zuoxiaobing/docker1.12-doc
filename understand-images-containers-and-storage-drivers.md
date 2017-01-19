# 理解镜像，容器和存储驱动

要想真正的理解docker的存储驱动，需要先了解docker镜像是如何构建和存储，以及容器如何使用镜像.

## 镜像与层

每个dockers镜像引用一组只读层。层被堆叠在一起以形成容器根文件系统.。下面的图表显示了通过4层堆叠的Ubuntu 15.04镜像。

![](/assets/image-layers.jpg)

##### Docker存储驱动的作用就是:将这些分层的镜像文件堆叠起来，并且提供统一的视图.使容器的文件系统看上去和我们普通的文件系统没什么区别.

当创建一个新的容器的时候,实际上是在镜像的分层上新添加了一层container layer（容器层）.之后所有对容器产生的修改,实际都只影响这一层.

![](/assets/container-layers.jpg)

#### 注意

* 容器层：读写层\(可写层\)
* 镜像层：只读层

## 内容寻址存储

docker1.10引入了一个新的内容寻址存储模型。这是一个全新的方式来解决磁盘上的图像和层数据。此前，镜像和层的数据参使用随机生成的UUID储存。在新的模型中，这将被一个安全的内容散列替换.。

新的模式提高了安全性。它也有更好的共享层，允许更多镜像自由地共享他们的层。

下面这个图片
