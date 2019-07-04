# Docker -- Forbidden path outside the build context

## 一、问题描述

> 因为有一个文件夹，有多个镜像需要使用，所以我把这个文件夹放到与其它模块文件夹同级。

当我在模块中的 `Dockerfile` 中这样写：

```Dockerfile
COPY ../code/ /var/www/html/
# or
ADD ../code/ /var/www/html/
```

执行 `build` 就会获得：

```bash
ERROR: Service 'nginx' failed to build: COPY failed: Forbidden path outside the build context: ../code/ ()
# or
ERROR: Service 'nginx' failed to build: ADD failed: Forbidden path outside the build context: ../code/ ()
```

## 二、解决方法

### 1. 第一种方法

把 `../code/` 换成绝对路径 `～/Workspace/code/`。

这种方法虽然能解决问题，但是并不利于项目的迁移，以及与他人共享我的代码。

### 2. 第二种方法

我管理项目，用的是 Docker Compose。

`Docker` 修改成这样：

```Dockerfile
COPY ./code/ /var/www/html/
# or
ADD ./code/ /var/www/html/
```

我这样写，一定有人会想，我是不是把这个文件夹放到每个模块的文件夹里了。  
并不是，请继续看下去。

我还有一个 `docker-compose.yml` 文件：

```yaml
version: '3'

services:

  nginx:
    build:
      context: .
      dockerfile: ./nginx/Dockerfile

  ...

  php-fpm:
    build:
      context: .
      dockerfile: ./fpm/Dockerfile
```

通过 `docker-compose.yml` 中的设置，把根文件夹和 `Dockerfile` 分离开来。  
就是相当于把 `Dockerfile` 复制到根目录，在构建镜像。

这种方法，对我来说，算是一个比较合理的解决方法，比第一种方法要好很多。
