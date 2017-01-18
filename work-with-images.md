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







