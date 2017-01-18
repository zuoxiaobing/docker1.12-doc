# 第一步: 标记和推送镜像 {#第一步-标记和推送镜像}

1. 输入`docker images`命令来查看当前的镜像列表:

2. 找到docker-whale镜像的`image id`

   在这个例子中, id是`7d9495d03763`

   值得注意的是, Hub库显示的是库的名称而不是命名空间. 对于Docker Hub将docker-whale镜像与Docker Hub帐户相关联后, 你需要为它重命名为`YOUR_DOCKERHUB_USERNAME/docker-whale`. 您的帐户名称会在Docker Hub中显示镜像的命名空间. 你会在下一步标记镜像中做到这一点.

3. 使用`IMAGE ID`和`docker tag`命令来标记docker-whale镜像.

   命令看来起来像这样子:

   ![](http://img.blog.csdn.net/20160224154105327 "这里写图片描述")

   当然你的账户名称应该是你自己的, 所以, 你要在这个命令中使用自己的账户名称和镜像ID, 然后按回车.

   `$ docker tag 7d9495d03763 maryatdocker/docker-whale:latest`

4. 再次输入`docker images`命令来查看当前的镜像列表:

5. 使用`docker login`命令从命令行登陆Docker Hub

   登陆命令的格式是这样的:

   `docker login --username=yourhubusername --email=youremail@company.com`

   当命令返回响应是, 输入你的密码并按回车, 所以, 像这样:

   ```
   $ docker login --username=maryatdocker --email=mary@docker.com
   Password:

   WARNING: 
   login credentials saved in C:\Users\sven\.docker\config.json
   Login Succeeded


   ```

6. 输入`docker push`命令来推送你的镜像到Hub库

    $ docker push maryatdocker/docker-whale

7. 返回你在Docker Hub的页面查看你的新镜像

   ![](http://img.blog.csdn.net/20160224154845273 "这里写图片描述")

# 第二步: 拉取新镜像 {#第二步-拉取新镜像}

在这最后一节，你会拉取你刚刚推送到Docker Hub上的镜像。在此之前, 你需要从本地计算机中删除原始镜像. 如果你没有删除, Docker不会从Hub上拉取镜像 - 为什么这样呢？因为两个图像是相同的.

1. 将光标定位到Docker Quickstart Terminal窗口

2. 输入`docker images`来列出你当前在本地的镜像列表

3. 使用`docker rmi`删除`maryatdocker/docker-whale`和`docker-whale`镜像.

   你可以使用镜像ID或者名称删除

$ docker rmi -f 7d9495d03763

 $ docker rmi -f docker-whale

使用`docker run`命令从你的Hub库中拉取并加载新的镜像

$`docker run yourusername/docker-whale`

由于镜像不再在本地系统上，Dokcer要下载它。





