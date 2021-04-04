---
title: Docker安装Jenkins实现自动部署
date: 2020-11-28 22:51:08
description: # 描述
tags: 
  - Docker
---

暂无说明

<!-- more -->


## Jenkins安装

1. 拉取镜像

````
docker pull jenkinsci/blueocean
````

2. 查看镜像

````
docker images
````

3. 创建容器

````
docker run -d  \
--name jenkins -u root \
-p 13912:8080  \
--privileged=true \
-v /home/docker_data/jenkins/jenkins_home:/var/jenkins_home  \
jenkinsci/blueocean
````



## Jenkins初始化

1. 以root权限进入docker容器Jenkins

````
# 查看容器的CONTAINER ID
sudo docker container ls
# 8b96bbb88d0a 为上面查到的CONTAINER ID
sudo docker exec -ti -u root 8b96bbb88d0a bash
````

2. 容器内修改alpine镜像源为阿里云

````
sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
apk update
````

3. 容器内安装maven

````
apk add maven
apk add ansible
apk add ansible-doc
````

4. 查看maven和jdk安装位置

````
apk info -L maven  
例：/usr/share/java/maven-3

echo $JAVA_HOME
例：/opt/java/openjdk
````

5. 查看Jenkins初始化密码

````
find  / -name initialAdminPassword |xargs ls -l
# 查到路径为/var/jenkins_home/secrets/initialAdminPassword
cat  /var/jenkins_home/secrets/initialAdminPassword
````

6. 访问http://宿主机IP:13912 用上面查到的密码初始化

7. 汉化

````
1. 安装locale plugin，Localization:Chinese(Simplified)这两个插件。
2. 系统管理–>系统设置–>Locale 填入：zh_CN
````

## Jenkins全局配置

1. Maven配置

````
默认settings提供：Settings file in filesystem
# 找上面步骤查到的Maven路径
文件路径：/usr/share/java/maven-3/conf/settings.xml

默认全局settings提供：Global settings file on filesystem
# 找上面步骤查到的Maven路径
文件路径：/usr/share/java/maven-3/conf/settings.xml默认全局settings提供：Global settings file on filesystem
# 找上面步骤查到的Maven路径
文件路径：/usr/share/java/maven-3/conf/settings.xml
````

2. JDK配置

````
取消Install automatically
JDK别名：jdk
# 找上面步骤查到的JDK路径
JAVA_HOME：/opt/java/openjdk
````

3. MAVEN安装(与上面不同)

````
NAME：maven
MAVEN_HOME：/usr/share/java/maven-3
````

## 安装插件

1. 安装Subversion Plugin

2. 安装Maven Integration plugin

## 远程Tomcat配置

`````
# /home/application/apache-tomcat-9.0.40/webapps/host-manager/META-INF/context.xml 和 /home/application/apache-tomcat-9.0.40/webapps/manager/META-INF/context.xml注释掉以下代码

<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />

# /home/application/apache-tomcat-9.0.40/conf/tomcat-users.xml 添加以下代码
<role rolename="admin-gui"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<user username="manager" password="manager" roles="manager-gui,manager-script"/>
<user username="admin" password="admin" roles="admin-gui,manager-gui,manager-script"/>
`````

## 新建Maven项目

1. 源码管理 配置Subversion

````
Repository URL：你的项目在SVN服务器上的路径
Credentials：你的SVN账号密码
# 其他的保持默认
````

2. Build

````
Root POM：pom.xml
Goals and options：clean install
````

3. 构建后操作

````
# 选择Deploy war/ear to a container
WAR/EAR files：target\jenkinsTest-1.0-SNAPSHOT.war
# 打包到tomcat生成的文件名
Context path：jenkinsTest
Containers：Tomcat 9.x Remote
Credentials: 账号admin 密码admin(上面Tomcat配置)
# Tomcat所在服务器IP:项目访问端口号# Tomcat所在服务器IP:项目访问端口号
Tomcat URL: http://192.168.2.10:/
````

## 保存配置，点击立即构建



## 参考：

https://blog.csdn.net/u012763794/article/details/80943472

http://www.eryajf.net/701.html

https://cloud.tencent.com/developer/article/1461397

