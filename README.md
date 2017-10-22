# Docker 简介

## 基本定义

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

## 优势

1. 性能方面：不依赖于任何语言、框架或包括系统。几乎没有性能开销,可以很容易地在机器和数据中心中运行，大多数docker容器只需不到1秒即可启动。

2. 环境方面：让开发和生产环境一致，不会在出现“开发时一切都正常，肯定是运维的问题”现象。

3. 产品方面：提高测试和部署效率，给精益创业满意的开发周期。

# Docker 组件

## Docker 客户端与服务器

Docker 是一个客户-服务器（C/S）架构的程序。Docker客户端只需向Docker服务器或守护进程发出请求，守护进程或服务器将完成所有工作并返回结果。

## 镜像

镜像是Docker生命周期中的“构建”阶段。

## Registry

Registry用来保存用户构建的镜像，是Docker生命周期的“仓储和运输”阶段。

## 容器

容器是Docker的启动或执行阶段。可以这样说，容器是：

- 另一种镜像格式
- 一系列标准的操作
- 一个执行环境

和集装箱一样，Docker 在执行操作时，并不关心容器中到底塞进了什么，他不管里面是web服务器，还是数据库，或是应用程序服务器什么的。所有容器都按照相同的方式将内容装载进去。

# Docker 安装

## Ubuntu安装
  wget -qO- https://get.docker.com/ | sh
  
  curl -fsSL https://get.docker.com/ | sh
## mac安装

  https://docs.docker.com/installation/mac/

## TUNA安装(Ubuntu14.04)

可以使用清华的镜像进行安装，具体参考http://mirrors.tuna.tsinghua.edu.cn/help/docker/

首先信任 Docker 的 GPG 公钥:
```
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
```

再添加源
```
echo "deb https://mirrors.tuna.tsinghua.edu.cn/docker/apt/repo ubuntu-trusty main" | sudo tee /etc/apt/sources.list.d/docker.list
```

如果安装过lxc-docker，先删除
```
sudo apt-get remove lxc-docker*
```

安装docker
```
sudo apt-get update
sudo apt-get install docker-engine
```


# Docker 基本操作

## 基本信息

* 查看info: `docker info`
* docker --help 整体的帮助，显示所有指令
* docker command --help 针对特定指令的帮助文档

## docker run 创建容器

* -d: 后台运行容器
* -P: 直接将容器内部的端口映射出去
* -p 80:5000 将容器的5000端口映射成localhost的80端口
* --name 为容器命名
* -v 添加data volume: 比如 `docker run -d -P --name web -v /webapp training/webapp python app.py`
* -v host-dir:container-dir
  * 比如 ` docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py`  将host的`/src/webapp` 加载到容器的`/opt/webapp`上
  * MacOS上Docker只有权限共享/Users 目录，所以只能这样写 `docker run -v /Users/<path>:/<container path> ...`
  * Windows上 只有权限共享 `C:\Users`目录，所以只能这样写 `docker run -v /c/Users/<path>:/<container path> ...`
* -link 通过名称来进行2个容器的通信，不适用ip和端口，举例如下
```
  docker run -d --name database postgres
  docker run -d -P --name website --link database:db ngnix
```
最终实际上会在website的/etc/hosts里面创建一个指向db的快捷方式

举例：
- 交互式启动一个cotainer: `docker run -i -t ImageName`
- 交互式启动一个自定义命名的cotainer: `docker run --name ContianerName -i -t ImageName`
- 守护式启动一个cotainer: `docker run -d ImageName`
- 守护式启动一个自定义命名的cotainer: `docker run --name ContianerName -d ImageName`


## 查看容器
- docker ps -l: 最后一个启动的容器
- docker ps -a: 所有容器，包括不在运行的
- 查看容器中的进程: `docker top ContianerName/ContainerId`
- 查看容器中的log: `docker logs ContianerName/ContainerId`
- 查看容器中的log(follow模式): `docker logs -f ContianerName/ContainerID`
- docker inspect

## 容器的启动、停止与删除
- 重新启动一个已有的cotainer: `docker start ContianerName/ContainerId`
- 停止一个已运行的cotainer: `docker stop ContianerName/ContainerId`
- 删除一个cotainer: `docker rm ContianerName/ContainerId`
- 删除Docker中全部cotainer: docker rm `docker ps -a -q`

## 通过docker进行container内部操作
- 深入查看container的info: `docker info`
- 守护式操作容器: `docker exec -d ContianerName/ContainerId CommandText`
- 交互式操作容器: `docker exec -i -t ContianerName/ContainerId CommandText`

使用exec指令在容器里面启动一个新的进程，执行/bin/bash可以获得一个shell，比如
`docker exec -i -t [ContainerId] /bin`

## 通过docker进行image的操作
- 在客户端登陆Docker Hub: `docker login`
- 查看image列表: `docker images`
- 拉取image列表: `docker pull ImageName`
- 拉取特定的image: `docker pull ImageName:TagName`
- 搜索image: `docker search ImageName`
- commit image: `docker commit -m"llllllll" --author="XXXXX" ContainerID Username/ImageName`(官方不推荐)
- build image: `docker build -t TagName ContextPath`
- 查看image历史: `docker build history ImageID`
