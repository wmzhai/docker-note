# Docker Compose

Compose用来定义和运行多容器的Docker应用。 可以使用一个compose文件来配置应用的服务，然后使用一个指令来创建和启动所有文件。


一般通过3步来使用Compose
1. 使用Dockerfile来定义app的环境，从而可以被复制
2. 在docker-compose.yml中定义组成app的服务，从而他们可以在一个独立环境中运行
3. 最后，运行`docker-compose up`，则Compose会启动运行整个app


docker-compose.yml示例如下

```
version: '2'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```    
