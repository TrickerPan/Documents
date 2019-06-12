# Docker 中 Python 使用 Postgres

> Docker 中安装依赖，总是一件很麻烦的事。  
> 因为大多数情况，本机的环境都很健全。  
> 但是微服务把这些健全的东西差分开了，就变得残缺不堪。

Dockerfile 文件

```Dockerfile
FROM python:3.6-alpine

RUN apk add gcc python3-dev musl-dev postgresql-db
RUN pip install psycopg2
```
