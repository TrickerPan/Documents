# Ubuntu 创建新用户并赋予 sudo 权限

Ubuntu 中，关于用户操作的命令如下:

```bash
# 添加用户
$ useradd username
$ adduser username
# 删除用户
$ userdel username
$ deluser username
# 添加用户组
$ groupadd groupname
$ addgroup groupname
# 删除用户组
$ groupdel groupname
$ groupadd groupname
```

## 创建用户

```bash
# 首先要以 root 用户身份登录
root@ubuntu:~$ adduser tricker
Adding user `stob' ...
Adding new group `stob' (1000) ...
Adding new user `stob' (1000) with group `stob' ...
Creating home directory `/home/stob' ...
Copying files from `/etc/skel' ...
Enter new UNIX password: # 输入密码
Retype new UNIX password: # 再次输入密码
passwd: password updated successfully
Changing the user information for stob
Enter the new value, or press ENTER for the default
  Full Name []: Tricker Pan
  Room Number []:
  Work Phone []:
  Home Phone []:
  Other []:
Is the information correct? [Y/n] Y
root@ubuntu:~$ su tricker # 切换到新创建的用户
tricker@ubuntu:/root$ cd # 返回用户根目录
tricker@ubuntu:~$ pwd # 输出用户根目录
/home/tricker
```

## 赋予用户使用 sudo 命令的权限

```bash
# 首先要以 root 用户的身份登录
root@ubuntu:~$ usermod -aG sudo tricker
root@ubuntu:~$ su tricker # 切换到新创建的用户
tricker@ubuntu:/root$ cd # 返回用户根目录
tricker@ubuntu:~$ pwd # 输出用户根目录
/home/tricker
```
