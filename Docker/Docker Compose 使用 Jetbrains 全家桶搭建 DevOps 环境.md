# **Docker Compose 使用 Jetbrains 全家桶搭建 DevOps 环境**

```yaml
# docker-compose.yml
version: "3"

services:

  postgres:
    image: postgres:11-alpine
    restart: always
    ports:
      - "5432:5432"
    volumes:
      - "./postgres/data:/var/lib/postgresql/data"
    environment:
      POSTGRES_PASSWORD: my_password
    networks:
      database:
        aliases:
          - "postgres.tricker.org"

  mysql:
    image: mysql:8
    restart: always
    ports:
      - "3306:3306"
    volumes:
      - "./mysql/data:/var/lib/mysql"
    environment:
      MYSQL_ROOT_PASSWORD: my_password
    networks:
      database:
        aliases:
          - "mysql.tricker.org"

  mongo:
    image: mongo:4-xenial
    restart: always
    ports:
      - "27017:27017"
    volumes:
      - "./mongo/data:/data/db"
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: my_password
    networks:
      database:
        aliases:
          - "mongo.tricker.org"
  redis:
    image: redis:5-alpine
    restart: always
    ports:
      - "6379:6379"
    volumes:
      - "./redis/data:/data"
    networks:
      database:
        aliases:
          - "redis.tricker.org"

  gitlab:
    image: gitlab/gitlab-ce:latest
    restart: always
    hostname: "gitlab.tricker.org"
    ports:
      - "8888:8888"
      - "2222:22"
    volumes:
      - "./gitlab/config:/etc/gitlab"
      - "./gitlab/data:/var/opt/gitlab"
      - "./gitlab/logs:/var/log/gitlab"
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url "http://gitlab.tricker.org:8888"
        gitlab_rails["gitlab_shell_ssh_port"] = 2222
    networks:
      devops:
        aliases:
          - "gitlab.tricker.org"
  
  hub:
    image: jetbrains/hub:2018.4.11442
    restart: always
    ports:
      - "8080:8080"
    volumes:
      - "./hub/data:/opt/hub/data"
      - "./hub/conf:/opt/hub/conf"
      - "./hub/logs:/opt/hub/logs"
      - "./hub/backups:/opt/hub/backups"
    networks:
      devops:
        aliases:
          - "hub.tricker.org"

  upsource:
    image: jetbrains/upsource:2018.2.1291
    restart: always
    ports:
      - "8081:8080"
    volumes:
      - "./upsource/data:/opt/upsource/data"
      - "./upsource/conf:/opt/upsource/conf"
      - "./upsource/logs:/opt/upsource/logs"
      - "./upsource/backups:/opt/upsource/backups"
    networks:
      - devops
    depends_on:
      - hub
      - gitlab

  youtrack:
    image: jetbrains/youtrack:2019.1.52973
    restart: always
    ports:
      - "8082:8080"
    volumes:
      - "./youtrack/data:/opt/youtrack/data"
      - "./youtrack/conf:/opt/youtrack/conf"
      - "./youtrack/logs:/opt/youtrack/logs"
      - "./youtrack/backups:/opt/youtrack/backups"
    networks:
      - devops
    depends_on:
      - hub


  teamcity:
    image: jetbrains/teamcity-server
    restart: always
    ports:
      - "8111:8111"
    volumes:
      - "./teamcity/data:/data/teamcity_server/datadir"
      - "./teamcity/logs:/opt/teamcity/logs"
    networks:
      - devops
      - database
    depends_on:
      - postgres
      - mysql
      - gitlab

networks:
  devops:
  database:
```

> &nbsp;  
> hub、upsource、youtrack 这三个共享卷，需要在手动创建共享文件夹，并赋予权限
>
> ```bash
> # 在 docker-compose.yml 所在文件夹内执行。
> # 为每个容器创建文件夹
> $ mkdir hub upsource youtrack
> # 创建共享文件夹
> $ mkdir -p -m 750 hub/data hub/conf hub/logs hub/backups upsource/data upsource/conf upsource/logs upsource/backups youtrack/data youtrack/conf youtrack/logs youtrack/backups
> # 为所有文件夹赋权
> $ sudo chown -R 13001:13001 hub upsource youtrack
> ```
>
> &nbsp;
