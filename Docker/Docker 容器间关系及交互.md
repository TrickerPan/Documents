# Docker 容器间关系及交互

> **阅读指南：**  
>
> 1. 此文档使用变量的方式采用 unix shell 方式。例：`$(变量名称)`。
> 2. 因为 `Dockerfile` 中的关键字均是同级，所以示例分条编写。而 `docker-compose` 的配置文件 `docker-compose.yml` 采用的是 `yaml` 格式，具有严格的格式要求，所以把所有示例均放入 [2. services 关键字](#2.-services-关键字) 内。

|目的|时间|编辑人|
|---|---|---|
|创建并编辑文档|2019 年 04 月 11 日|潘星宇|
|编辑未完成的内容|2019 年 04 月 12 日|潘星宇|

+ [一、Dockerfile 二三事](#一、Dockerfile-二三事)
  + [1. FROM 关键字](#1.-FROM-关键字)
  + [2. ADD 和 COPY 关键字](#2.-ADD-和-COPY-关键字)
  + [3. RUN 、CMD 和 ENTRYPOINT 关键字](#3.-RUN-、CMD-和-ENTRYPOINT-关键字)
  + [4. ENV 关键字](#4.-ENV-关键字)
  + [5. WORKDIR 关键字](#5.-WORKDIR-关键字)
  + [6. USER 关键字](#6.-USER-关键字)
  + [7. EXPOSE 关键字](#7.-EXPOSE-关键字)
+ [二、docker-compose.yml 二三事](#二、docker-compose-二三事)
  + [1. version 关键字](#1.-version-关键字)
  + [2. services 关键字](#2.-services-关键字)
  + [3. image 和 build 关键字](#3.-image-和-build-关键字)
  + [4. ports 关键字](#4.-ports-关键字)
  + [5. env_file 和 environment 关键字](#5.-env_file-和-environment-关键字)
  + [6. restart 关键字](#6.-restart-关键字)
  + [7. links 和 depends_on 关键字](#7.-links-和-depends_on-关键字)
  + [8. Swarms 集群及负载均衡](#8.-Swarms-集群及负载均衡)
+ [三、IMAGE 镜像 和 CONTAINER 容器 的区别](#三、IMAGE-镜像-和-CONTAINER-容器-的区别)

------

## 一、`Dockerfile` 二三事

`Dockerfile` 文件，是为配置并创建镜像而存在的，可以指定基础镜像、镜像的用户、复制文件或文件夹到镜像内等。  
下面是一些常用的 `Dockerfile` 关键字：

### 1. `FROM` 关键字

`FROM $(镜像名称):$(版本)` 导入一个基础镜像作为本镜像的底层。例：

```Dockerfile
FROM openjdk:8
```

`Docker Hub` 的镜像库中，基础镜像通常都是 `Ubuntu` 或 `CentOS`，无论哪个都是上百 MB。  
有一些镜像，为我们提供了新的选择 —— `Apline` 。一个面向安全应用的轻量级系统。使用时，只需在镜像版本后添加 `-apline` 标志。例：`FROM openjdk:8-apline` 。

> 更多镜像，请浏览 [https://hub.docker.com/](https://hub.docker.com/)

### 2. `ADD` 和 `COPY` 关键字

`ADD` 和 `COPY` 都是往镜像内添加文件或文件夹的命令，但是 `ADD` 比 `COPY` 更高级，两者用法也存在着一定的异同点。如下：

+ 两者使用方式一样，都可以实用 shell 格式 或者 exec 格式。例：`ADD $(源路径) $(目标路径)` 或 `ADD ["源路径",..."目标路径"]`
+ 源路径 只能是 `Dockerfile` 文件所在目录内的相对路径。
+ 目标路径 可以是镜像内的绝对路径，也可以是 `WORKDIR` 的相对路径。
+ 源路径 可以实用符合 `GO` 语言的 `filepath.Match` 规则的通配符去加载文件或文件夹。如：`*` `?` ...
+ `ADD` 的源路径可以使一个 URL ，但是 `ADD` 命令并不支持认证方法，所以如果需要从网络获取资源的话，尽量使用 `RUN curl` 命令更加安全实用。
+ `ADD` 命令，如果源路径指向的是一个 tar 格式的压缩文件，那么将会自动解压，并添加到镜像内。
+ `ADD` 命令，还会为添加的文件，自动赋予 600 可读写权限。所以，当你需要添加 jar 包一类的可执行文件时，使用 `ADD` 命令再方便不过了；但是，当你添加一些普通的文件时，还是实用 `COPY` 命令，添加只读文件更安全些。

### 3. `RUN` 、`CMD` 和 `ENTRYPOINT` 关键字

`RUN` 、`CMD` 和 `ENTRYPOINT` 命令的功能，都是执行 shell 格式 或者 exec 格式 的命令。但是三者在使用环境上，却有很大的区别。如下：

+ `RUN` 用于构建镜像时，执行命令。在以后启动容器时，并不会再次执行 `RUN` 的命令。
+ 当 `Dockerfile` 内含有多个 `CMD` 命令时，只有最后一个会被执行。
+ 当 `Dockerfile` 内含有多个 `ENTRYPOINT` 命令时，也只有最后一个会被执行。
+ `CMD` 和 `ENTRYPOINT` 均用于在容器启动时，执行命令。但两者存在一定的区别。如下：
  + `CMD` 命令，在使用 `docker run` 命令加上参数启动容器时，则会覆盖掉 `CMD` 后面的命令。
  + `ENTRYPOINT` 命令，无论在什么情况下启动容器，都会执行。除非执行 `docker run --entrypoint` 命令，硬性替换掉 `ENTRYPOINT` 命令

### 4. `ENV` 关键字

设置镜像内的环境变量。例：

```Dockerfile
ENV BUILD_NAME development
```

### 5. `WORKDIR` 关键字

设置镜像的工作目录。例：

```Dockerfile
RUN ["mkdir", "app"]
WORKDIR app
```

### 6. `USER` 关键字

设置镜像系统的执行用户。例：

```Dockerfile
RUN ["adduser", "-D", "apprunner"]
USER apprunner
```

> 为容器的安全性考虑，建议为每个容器设置不同的用户

### 7. `EXPOSE` 关键字

设置镜像允许外部访问的端口，默认暴露 80 端口。例：

```Dockerfile
EXPOSE 8080
```

## 二、`docker-compose` 二三事

`docker-compose` 命令，是执行 `docker-compose.yml` 文件内的配置，用于管理多个镜像与容器的配置及启动，省略了使用 `docker build` 和 `docker run` 命令一个个创建镜像和启动容器繁琐。  
下面是一些常用的 `docker-compose.yml` 关键字：

### 1. `version` 关键字

`docker-compose` 目前拥有 3 个版本。因为每个版本间，均不互相兼容，所以除了第一个版本，其它版本均需执行此关键字。例：

```Dockerfile
version "3"
```

> 此文档中，只采用第三个版本，如想研究前两个版本，请查看 [https://docs.docker.com/compose/overview/](https://docs.docker.com/compose/overview/)

### 2. `services` 关键字

配置微服务集群，`services` 关键字内可包含一个或无数个服务。例：

```yaml
version: "3"

services:
  mainservice:
    image: ubuntu:18
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
      restart_policy:
        condition: on-failure
    environment:
      BUILD_NAME: development
      SOME_CONFIG: myconfig.yml
    ports:
      - 80:8080
      - 443:4430
    depends_on:
      - mysql:mysqlserver
      - redis
    links:
      - subservcie
  subservice:
    build: sub/
    restart: always
    env_files: .env.dev
    links:
      - mysql
      - redis
  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_USER: sample
      MYSQL_PASSWORD: sample
      MYSQL_DATABASE: sample_db
  redis:
    image: redis:4.0-apline
    restart: always
```

### 3. `image` 和 `build` 关键字

`image` 和 `build` 关键字都是用来设置镜像的。不同点是：

+ `image` 是从 `Docker Hub` 的镜像库中导入镜像，使用方法和 `Dockerfile` 的 `FROM` 关键字相同（[FROM 关键字](#1.-FROM-关键字)）。
+ `build` 是从本地某个文件夹内的 `Dockerfile` 创建镜像，指定的路径是包含 `Dockerfile` 的文件夹。

> 示例如 [2. services 关键字](#2.-services-关键字) 内的示例所示。

### 4. `ports` 关键字

设置容器映射到本地的端口，可以配置一个或多个端口映射，格式为 `$(本地端口):$(容器端口)` 。

> 示例如 [2. services 关键字](#2.-services-关键字) 内的示例所示。

### 5. `env_file` 和 `environment` 关键字

`env_file` 和 `environment` 均是设置环境变量的关键字，但是方式不用。

+ `env_file` 是从 `docker-compose.yml` 文件所在的目录，或者 `build` 关键字所指定的目录内导入相应的文件，并读取文件中配置的环境变量，加载到容器内。
+ `environment` 是直接配置一条或多条环境变量到容器内。
+ 环境变量的命名，均采用 大写字母加下划线（_）的方式。

> 示例如 [2. services 关键字](#2.-services-关键字) 内的示例所示。

### 6. `restart` 关键字

配置容器运行中因任何问题停止后的重启方式。通常设置为 `always` —— 总是重启。

> 示例如 [2. services 关键字](#2.-services-关键字) 内的示例所示。

### 7. `links` 和 `depends_on` 关键字

`links` 和 `depends_on` 关键字均是配置容器间的链接关系。使用方式相同，但是应用方式不同。  
共同点：

+ 设置格式均为 `$(服务名称):$(自定义名称)` 或 `$(服务名称)`
+ 服务名称必须是 `services` 关键字内配置的服务
+ 如果配置了自定义名称，则可在容器内使用 `$(自定义名称):端口/$(资源路径)` 的方式访问已配置的容器。
+ 如果未配置自定义名称，则使用 `$(服务名称):端口/$(资源路径)` 的方式访问已配置的容器。

不同点：

+ `depends_on` 配置后，此容器一定在已配置的容器启动成功之后才会启动。
+ `links` 配置后，容器的启动顺序并无固定关系。

> 示例如 [2. services 关键字](#2.-services-关键字) 内的示例所示。

### 8. `Swarms` 集群及负载均衡

`deploy` 关键字，配置此服务的部署方式。  
`replicas` 关键字，配置此服务部署的容器数量。
`resources` 关键字，配置此服务部署的每个容器所占用的资源。
`limits` 关键字，配置此服务部署的每个容器所占用的资源限制。
`cpus` 关键字，配置此服务部署的每个容器所占用的 cpu 百分比。
`restart_policy` 关键字，配置此服务内的所有容器的重启策略。
`condition` 关键字，配置此服务内的所有容器的重启条件。

> 示例如 [2. services 关键字](#2.-services-关键字) 内的示例所示。

## 三、`IMAGE` 镜像 和 `CONTAINER` 容器 的区别

+ 镜像是容器运行的基础。容器基于镜像，镜像提供给容器服务。
+ 一个镜像可以被多个容器运行。
+ 容器就是我们的微服务器。

[返回顶部](#docker-容器间关系及交互)
