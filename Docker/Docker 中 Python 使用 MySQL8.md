# Docker 中 Python 使用 MySQL8

> MySQL8 版本中，密码验证方式改成了 `caching_sha2_password` 。与之相对的，程序上，想要连接 MySQL 数据库，也需要对密码做相应的处理。  
> Python 想要连接 MySQL 就需要一个加密包 `cryptography`。

在本机安装，没什么好说的，缺什么，装什么就行了。重点内容是在 Docker 中，如何添加依赖，以及需要那些依赖。

Dockerfile 文件

```Dockerfile
FROM python:3.6-alpine

RUN apk add gcc python3-dev musl-dev libffi-dev openssl-dev
RUN pip install cryptography
```
