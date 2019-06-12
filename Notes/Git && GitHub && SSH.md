# Git && GitHub && SSH

## 1. 查看电脑是否持有SSH密钥

```bash
$ ls -al ~/.ssh
# 如果持有，请直接跳到第 3 步。
# 没有的同学，请继续阅读。
```

## 2. 生成 SHH 密钥

```bash
$ ssh-keygen -t rsa -C "your_email@example.com"
# -t rsa: 密钥分为两种，RSA 和 DSA，如果不指定，默认生成RSA
# -C: 写入一段注释
Generating public/private rsa key pair.
Enter file in which to save the key (/home/Tricker/.ssh/id_rsa): 
# 回车，选择默认目录即可
Enter passphrase (empty for no passphrase):
# 继续回车，创建空密码
Enter same passphrase again: 
# 再次回车，确认密码
Your identification has been saved in /home/root/.ssh/id_rsa.
Your public key has been saved in /home/root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:M/saFstK4iCZp5pYw15cwvXD9rQpjd6JYHrrTsQIeHY your_email@example.com
The key's randomart image is:
+---[RSA 2048]----+
|   +  E   . =O +=|
|  . +      + o+Bo|
|     .    +o+..oo|
|     ..ooo+.     |
|       .S++  o   |
|       ooo + o   |
|      . +oO =    |
|       ... + .   |
|        o+.  .   |
+----[SHA256]-----+
```

## 3. 配置 SSH 及 GitHub

```bash
$ eval "$(ssh-agent -s)"
# ssh-agent: 是一个密钥管理器
# -s: 以 Bourne shell 风格启动
Agent pid 26222
# 返回 agent 的 pid
$ ssh-add ~/.ssh/id_rsa
# 把密钥交给 agent
# Ps.如果你刚刚设置了 SSH 密码，这里则会让你输入密码
$ cat ~/.ssh/id_rsa.pub
# 抓取密钥
ssh-rsa AACEAAA10KBGrZK3ADAQABAAABAQDkEVfuh3ooH1qzGPIPETOuIx2P/PGmqE5+7HLYW6l8opcW3Iw198prDKDNt27lOE5iQ7bph67QdnFU9TL8KhdUSXQwCuhAo4gY9v26C8V2LcwK8TnOr+3hYp5PtCNwq0zIFSqc9XFdOzAOzvgXT/r13XCXSF0DJjFEGyMcUrd0EgQjn8EzvbdUj7JItepifnF9ecVcnuMvCDweMJkHxMAB3NzaC1yc2lH8qEET50c/JYz21u0mXo+1m5sTFXs3VAEMSv0Q8TZZbelWJq3qIITg+KklodvdRyWZqb/Ax5UXfaERmwY9d/RuZagM6pLi5R6TFrq70epvB7a7N6tlfhIbdl your_email@example.com
# 请不要忘记复制密钥
```

接下来登陆你的 [GitHub](https://github.com/settings/keys)，头像 **=> Settings => SSH and GPG keys**
![GitHub 截图](/static/images/github_add_ssh_key.png)

## 4. 接下来就可以用 Git 托管项目到 GitHub 上了

```bash
$ git remote add origin git@github.com:your_github_username/your_object.git
# 把项目添加到托管仓库
$ git push -u origin master
# 把项目推送到托管仓库
# 这里会要求你输入你 GitHub 的用户名及密码
```
