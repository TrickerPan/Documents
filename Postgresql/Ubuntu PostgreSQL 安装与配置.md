# Ubuntu PostgreSQL 安装与配置

## 一、查看 Ubuntu 版本

```bash
$ lsb_release -cs
bionic  # 这里是我的 Ubuntu 版本
```

## 二、创建 apt 包管理仓库地址

```bash
sudo touch /etc/apt/sources.list.d/pgdg.list
sudo vim /etc/apt/sources.list.d/pgdg.list
```

在打开的文件内添加一行 `deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main`。
此处的 `bionic` 是我的 Ubuntu 版本，请换成您自己的 Ubuntu 版本。

> **备注**
>
> 关于 vim 的使用，这里就不再复述。~~如有不懂，请找度娘~~。

## 三、导入仓库签名秘钥

```bash
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

## 四、安装 PostgreSQL

```bash
$ sudo apt update
...
$ sudo apt install postgresql-10
...
```

## 五、设置 postgres 的密码

安装 PostgreSQL 后，会自动创建 Ubuntu 的 postgres 用户和 PostgreSQL 的 postgres 用户。现在，我们要设置 postgres 这个用户的密码和 PostgreSQl 内 postgres 用户的密码。请保持这两个密码的一致性，避免未知异常。

```bash
$ sudo passwd -d postgres  # 清除原有 postgres 用户密码
passwd: password expiry information changed.  # 密码清除成功
$ sudo -u postgres passwd
Enter new UNIX password:   # 输入新密码
Retype new UNIX password:   # 确认新密码
passwd: password updated successfully  # 密码设置成功
$ sudo -u postgres psql
psql (10.7 (Ubuntu 10.7-1.pgdg18.04+1))
Type "help" for help.

postgres=# ALTER USER postgres WITH PASSWORD 'yourpassword';  # 输入你的新密码，要和前面的密码一致
ALTER ROLE
postgres=# \q  # 退出
```

## 六、创建数据库储存空间

官方文档推荐的位置是 `/user/local/pgsql/data` 或 `/var/lib/pgsql/data`。
根据官方文档的步骤

```bash
$ sudo -u postgres initdb -D /user/local/pgsql/data
sudo: initdb: command not found
```

查找一下 `initdb` 命令 `sudo find / -name initdb`。发现 `initdb` 在 `/usr/lib/postgresql/10/bin/initdb`。
那么重试刚刚的命令。

```bash
$ sudo -u postgres /usr/lib/postgresql/10/bin/initdb -D /usr/local/pgsql/data
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locales
  COLLATE:  en_US.utf8
  CTYPE:    en_US.utf8
  MESSAGES: en_US.utf8
  MONETARY: en_US.UTF-8
  NUMERIC:  en_US.UTF-8
  TIME:     en_US.UTF-8
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

creating directory /usr/local/pgsql/data ... initdb: could not create directory "/usr/local/pgsql": Permission denied
```

真是无语，又提示我没有权限。
我选择先创建目录，并赋予权限

```bash
$ sudo mkdir /usr/local/pgsql
$ sudo chown postgres:postgres /usr/local/pgsql
$ sudo -u postgres /usr/lib/postgresql/10/bin/initdb -D /usr/local/
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locales
  COLLATE:  en_US.utf8
  CTYPE:    en_US.utf8
  MESSAGES: en_US.utf8
  MONETARY: en_US.UTF-8
  NUMERIC:  en_US.UTF-8
  TIME:     en_US.UTF-8
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

creating directory /usr/local/pgsql/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    /usr/lib/postgresql/10/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
```

## 七、启动数据库

```bash
$ sudo -u posgres /usr/lib/postgresql/10/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
waiting for server to start..../bin/sh: 1: cannot create logfile: Permission denied
 stopped waiting
pg_ctl: could not start server
Examine the log output.
```

还是没有权限的问题，我决定去 postgres 用户的根目录去执行这条命令。

```bash
$ cd /var/lib/postgresql
$ sudo -u postgres /usr/lib/postgresql/10/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
waiting for server to start.... stopped waiting
pg_ctl: could not start server
Examine the log output.
```

权限有了，但是 PostgreSQL 数据库服务已经启动了，让我来重启它。

```bash
$ sudo /etc/init.d/postgresql stop
[ ok ] Stopping postgresql (via systemctl): postgresql.service.
$ sudo -u postgres /usr/lib/postgresql/10/bin/pg_ctl -D /usr/local/pgsql/data -l logfile start
ctl -D /usr/local/pgsql/data -l logfile start
waiting for server to start.... done
server started
```
