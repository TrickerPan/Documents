# Docker 中搭建 Laravel 生产环境

## 一、目录结构

    demo
    ├-- code                 # Laravel 代码
    ├-- fpm
    ├-- mysql
    |   └-- data             # mysql 数据共享卷
    ├-- nginx
    |   ├-- conf.d
    |   |   └-- default.conf # nginx 配置文件
    |   ├-- logs             # nginx 日志共享卷
    |   └-- ssl              # nginx ssl 文件共享卷
    ├-- redis
    |   └-- data             # redis 数据共享卷
    └-- docker-compose.yml   # Docker Compose 配置文件

## 二、`docker-compose.yml`

我们先来解读一下这个文件

```yaml
version: '3'

services:
  nginx:
    image: nginx:1.16-alpine
    ports:
      - '80:80'
      - '443:443'
    volumes:
      - './code:/var/www/html'
      - './nginx/conf.d:/etc/nginx/conf.d'
      - './nginx/ssl:/etc/nginx/ssl'
      - './nginx/logs:/var/log/nginx'
    command: ['nginx-debug', '-g', 'daemon off;']
    depends_on:
      - php-fpm

  php-fpm:
    image: php:7.2-fpm-alpine
    volumes:
      - './code:/var/www/html'
    depends_on:
      - mysql
      - redis

  mysql:
    image: mysql:5.7
    ports:
      - '3306:3306'
    volumes:
      - './mysql/data:/var/lib/mysql'
    environment:
      MYSQL_ROOT_PASSWORD: Tricker110112
      MYSQL_DATABASE: blog

  redis:
    image: redis:5-alpine
    volumes:
      - './redis/data:/data'
```

1. nginx 和 php-fpm 都需要把 Laravel 的 code 放进去，而且要目录一致。  
2. nginx 依赖于 php-fpm。
3. nginx 中的 ssl 是可有可无的，具体看个人需求。
4. php-fpm 依赖于 mysql 和 redis。
5. 这里注意一下，nginx 是不需要访问 mysql 和 redis 的。
6. mysql 这里我选用的是 5.7 版本。没有选用 8.0 的原因，请参阅 [PHP: 需求 - Manual](https://php.net/manual/zh/mysqli.requirements.php)。
7. 访问时，出现 `Promission Denied`，执行 `sudo chmod -R 777 code/storage` 命令。

### 三、nginx

这里只有一个 `default.conf` 文件需要注意一下。

```nginx
server {
    listen 80;
#     listen 443 ssl http2;
    server_name .demo.test;
    root "/var/www/html/public";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log /var/log/nginx/demo-access.log combined;
    error_log  /var/log/nginx/demo-error.log error;

    sendfile off;

    client_max_body_size 100m;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php-fpm:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
        fastcgi_connect_timeout 300;
        fastcgi_send_timeout 300;
        fastcgi_read_timeout 300;
    }

    location ~ /\.ht {
        deny all;
    }

#     ssl_certificate     /etc/nginx/ssl/demo.crt;
#     ssl_certificate_key /etc/nginx/ssl/demo.key;
}
```

1. 如果需要 ssl 使用 https 就去掉注释。
2. `fastcgi_pass php-fpm:9000;` 这一行中的 `php-fpm` 使用的是 `docker-compose.yml` 中，nginx 容器依赖的 php-fpm。

## 四、php-fpm

运行了所有容器之后，我们还需要进入 php-fpm 容器中进行一些操作。

```bash
$ docker-compose exec demo_php-fpm_1 /bin/sh
# 进入容器中
/var/www/html $ cp $PHP_INI_DIR/php.ini-development $PHP_INI_DIR/php.ini
/var/www/html $ docker-php-ext-install -j$(nproc) bcmath pdo_mysql
```

## 待补充
