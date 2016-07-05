# Dockerfile

Dockerfile包含创建镜像所需要的全部指令。基于在Dockerfile中的指令，我们可以使用Docker build命令来创建镜像。在Dockerfile文件中这些命令的顺序就是它们被执行的顺序。

## 常用指令

Dockerfile支持支持的语法命令如下：
```
INSTRUCTION argument
```
指令不区分大小写，但是，命名约定为全部大写。

### FROM

所有Dockerfile都必须以FROM命令开始，FROM命令指定镜像基于哪个基础镜像创建。

具体语法如下：
```
FROM <image name>
```
例如
```
FROM ubuntu
```

### MAINTAINER

设置该镜像的作者。

语法如下：
```
MAINTAINER <author name>
```

### RUN
在shell或者exec的环境下执行的命令，就和在shell里面执行的一样。
RUN指令会在新创建的镜像上添加新的层面，接下来提交的结果用在Dockerfile的下一条指令中。

语法如下：
```
RUN command
```

每个run指令都是在image的top writable layer执行一个commit，所以最好使用 `&&` 把连续的指令连接起来，比如

```
RUN apt-get update && apt-get install -y curl vim openjdk-7-jdk
```


### ADD
复制文件指令。它有两个参数<source>和<destination>。
destination是容器内的路径。source可以是URL或者是启动配置上下文中的一个文件。

语法如下：
```
ADD src destination
```

### CMD

容器默认的执行命令。
Dockerfile只允许使用一次CMD指令，使用多个CMD会抵消之前所有的指令，只有最后一个指令生效。

CMD有三种形式，建议使用数组形式：
```
CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
```

### ENTRYPOINT
配置给容器一个可执行的命令，每次使用镜像创建容器时一个特定的应用程序可以被设置为默认程序。
该镜像每次被调用时仅能运行指定的应用。
类似于CMD，Docker只允许一个ENTRYPOINT，多个ENTRYPOINT会抵消之前所有的指令，只执行最后的ENTRYPOINT指令。

语法如下：
```
ENTRYPOINT ["executable", "param1","param2"]
ENTRYPOINT command param1 param2
```

### EXPOSE

指定容器在运行时监听的端口。

语法如下：
```
EXPOSE <port>;
```


### WORKDIR

指定RUN、CMD与ENTRYPOINT命令的工作目录。

语法如下：
```
WORKDIR /path/to/workdir
```

### ENV
设置环境变量。它们使用键值对，增加运行程序的灵活性。

语法如下：
```
ENV <key> <value>
```

### USER
镜像正在运行时设置一个UID。

语法如下：
```
USER <uid>
```

### VOLUME

授权访问从容器内到主机上的目录。语法如下：
```
VOLUME ["/data"]
```

## [最佳实践](http://crosbymichael.com/dockerfile-best-practices.html)

### 在Dockerfile起始处使用同样的指令以利用缓存

每个dockerfile的指令在image做出一些修改，并作为后继指令的基础。 如果image具有相同的parent和指令，则会复用缓存里的image。

为了有效利用缓存，需要保持Dockerfile前面一致，仅修改后面部分。比如所有的Dockerfile保留如下前缀，一旦MAINTAINER这样的小改变发生也会迫使docker运行RUN来更新apt，而不是使用缓存。

```
FROM ubuntu
MAINTAINER Michael Crosby <michael@crosbymichael.com>

RUN echo "deb http://archive.ubuntu.com/ubuntu precise main universe" > /etc/apt/sources.list
RUN apt-get update
RUN apt-get upgrade -y
```

###  当构建镜像时使用可理解的标签，以便更好地管理镜像

除非是实验性的build，一般都需要传递-t参数来构建带tag的镜像。一个简单的可读tag会帮助你管理每个创建的镜像。

```
docker build -t="crosbymichael/sentry" .
```

###  避免在Dockerfile中映射公有端口

docker的两个重要概念是复用性和可移植性，镜像应该可以在任何主机上运行任何次数。
使用Dockerfile你可以映射私有和公共端口，不过你不应该在Dockerfile里面来映射，否则你只能有一个运行实例。
正常实在运行image时通过-p参数来指定容器的端口。

```
# private and public mapping
EXPOSE 80:8080

# private only
EXPOSE 80
```

###  CMD与ENTRYPOINT命令请使用数组语法


###  ENTRYPOINT和CMD最好放在一起使用
