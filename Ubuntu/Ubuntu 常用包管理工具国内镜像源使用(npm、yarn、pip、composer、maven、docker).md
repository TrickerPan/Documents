# **Ubuntu 常用包管理工具国内镜像源使用(npm、yarn、pip、composer、maven、docker)**

> 排序先后顺序依据配置的 **难易** 程度：**简** > **繁**

+ [npm](#1-npm)
+ [yarn](#2-yarn)
+ [pip](#3-pip)
+ [composer](#4-composer)
+ [maven](#5-maven)
+ [docker](#6-docker)

## **1. npm**

镜像库地址：[https://registry.npm.taobao.org](https://registry.npm.taobao.org)

```bash
# 1. 临时使用
$ npm install <PACKAGE_NAME> --registry https://registry.npm.taobao.org

# 2. 持久使用
$ npm config set registry https://registry.npm.taobao.org

# 3. 查看镜像库地址
$ npm config get registry
```

## **2. yarn**

镜像库地址：[https://registry.npm.taobao.org](https://registry.npm.taobao.org)

```bash
# 1. 临时使用
$ yarn save <PACKAGE_NAME> --registry https://registry.npm.taobao.org

# 2. 持久使用
$ yarn config set registry https://registry.npm.taobao.org

# 3. 查看镜像库地址
$ yarn config get registry
```

## **3. pip**

镜像库地址：[https://pypi.tuna.tsinghua.edu.cn/simple](https://pypi.tuna.tsinghua.edu.cn/simple)

```bash
# 1. 临时使用
$ pip install -i https://pypi.tuna.tsinghua.edu.cn/simple <PACKAGE_NAME>

# 2. 持久使用(pip 版本 >= 10.0.0)
$ pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 3. 查看镜像库
$ pip config get global.index-url
```

## **4. composer**

镜像库地址：[https://packagist.phpcomposer.com](https://packagist.phpcomposer.com)

```bash
# 1. 在当前项目中使用
$ composer config repo.packagist composer https://packagist.phpcomposer.com

# 2. 在全局中使用
$ composer config -g repo.packagist composer https://packagist.phpcomposer.com

# 3. 查看镜像库
$ composer config -gl
```

## **5. maven**

镜像库地址：[https://maven.aliyun.com/repository/public](https://maven.aliyun.com/repository/public)

```bash
# 1. 在当前项目中使用
$ vim pom.xml

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    ...

    <repositories>
        <repository>
            <id>aliyunmaven</id>
            <name>aliyun public repository</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
        </repository>
     </repositories>

    ...

</project>

# 2. 在全局中使用
$ sudo cp /etc/maven/setting.xml ~/.m2/
$ sudo chown $USER:$USER ~/.m2/setting.xml
$ vim ~/.m2/setting.xml

<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

    ...

    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>aliyun public repository</name>
        <url>https://maven.aliyun.com/repository/public</url>
    </mirror>

    ...

</settings>
```

## **6. docker**

镜像库地址：[https://docker.mirrors.ustc.edu.cn](https://docker.mirrors.ustc.edu.cn)

```bash
# 1. 临时使用
$ docker pull docker.mirrors.ustc.edu.cn/<REGISTRY>/<IMAGE>

# 2. 持久使用
$ sudo vim /etc/docker/daemon.json

{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}

$ sudo systemctl restart docker
```
