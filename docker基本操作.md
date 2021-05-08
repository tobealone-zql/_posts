---
title: Docker基本使用
description: "Docker基本使用"
description: # 描述
tags: 
  - Docker
---

暂无说明

<!-- more -->

### CentOS7操作docker命令

#### 安装

````
yum -y install docker
````

#### 停止Docker

````
service docker stop
````

#### 修改镜像源为国内镜像 

````
vi /etc/docker/daemon.json
# daemon.json文件加入以下代码
{
"registry-mirrors": ["http://hub-mirror.c.163.com"]
}
````

#### Docker启动

````
service docker start
````

#### Docker设置开机启动

````
sudo systemctl enable docker
````

#### 查看版本

````
docker version
````

### Docker基本操作命令

#### 安装Mysql5.7镜像

1. 搜索可用镜像

````
docker search mysqldocker search mysql
````

2. 拉取镜像

````
docker pull mysql:5.7
````

3. 创建容器

**5.7版本**

````
docker run -p 3306:3306 --privileged=true -v /home/docker_data/mysql57/logs:/logs -v /home/docker_data/mysql57/data:/var/lib/mysql -v /home/docker_data/mysql57/config/my.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf -e MYSQL_ROOT_PASSWORD=long55555 -d mysql:5.7
````

````
docker run -p 3306:3306 --privileged=true --name mysql \
-v /home/app/docker/mysql57/log:/var/log/mysql \
-v /home/app/docker/mysql57/data:/var/lib/mysql \
-v /home/app/docker/mysql57/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7
````





**8版本**

````
docker run -it -d -u root \
      --privileged=true \
      --restart=always \
      -v /home/docker_data/mysql8/data/:/var/lib/mysql \
      -v /home/docker_data/mysql8/conf/my.cnf:/etc/my.cnf \
      --name mysql8 \
      -e MYSQL_ROOT_PASSWORD=root  \
      -p 13911:3306 mysql:latest \
      --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
````



#### 安装Tomcat

1. 搜索可用镜像

````
docker search tomcat
````

2. 拉取最新镜像

````
docker pull tomcat
````

3. 创建容器

````
docker run --name tomcat -d \
-p 12713:8080 \
-v /home/docker_data/tomcat/webapps:/usr/local/tomcat/webapps \
tomcat
````

#### 安装oracle12C

1. 拉取镜像

````
docker pull sath89/oracle-12c
````

2. 创建容器

````
mkdir -m 777 /home/docker_data/oracle12c

docker run -d --privileged=true  --name oracle_12c -p 13914:1521  -v /home/docker_data/oracle12c:/u01/app/oracle sath89/oracle-12c
````

3. navicat连接初始参数 

````
docker exec -it oracle_12c /bin/bash  
#切换到root用户，这是在容器内部切换
su root
#password:helowin
#编辑环境变量文件profile /etc/profile
vi /etc/profile
#文件末尾加入
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
export ORACLE_SID=helowin
export PATH=$ORACLE_HOME/bin:$PATH
#生效环境变量文件
source /etc/profile
#切换到Oracle账户，这是在容器内部切换
su oracle
#启动Oracle命令行工具
sqlplus /nolog
#连接到sysdba账户，设置用户名，密码
connect sysdba / as sysdba
startup
alter user system identified by oracle;
alter user sys identified by oracle;
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
#退出命令行工具
quit
#退出容器
exit
#查看运行的容器
docker ps


````

4. oracle12安装参考教程

```
- https://juejin.cn/post/6844904103605174279
- https://blog.csdn.net/L_zsen/article/details/106066269?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare
```

- 安装11版本

````
docker run  -d --privileged=true   -p 1521:1521 --name oracle_11g registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g


docker run -d --privileged=true  --name oracle_12c -p 13914:1521  -v /home/docker_data/oracle12c:/u01/app/oracle sath89/oracle-12c
````





#### 查看所有容器

````
docker ps -a
````

#### 启动容器

````
docker start  ** (CONTAINER ID)
````

#### 停止容器

````
docker stop  ** (CONTAINER ID)
````

#### 进入容器

````
docker exec -it ** (CONTAINER ID) /bin/bash 
docker exec -it 6de6f9e1152a /bin/bash 
````

#### 删除容器

````
docker rm -f ** (CONTAINER ID)
````

#### 查看容器日志

````
# 10为后10条 s12为容器名字
sudo docker logs -f -t --tail 10 s12
# 查看最近30分钟日志
docker logs --since 30m CONTAINER_ID
````

#### 查看镜像

````
docker images
````

#### 删除镜像

````
docker rmi ** (IMAGE ID)
````

#### 删除未使用镜像

````
docker image prune -a(删除所有没有用的镜像，而不仅仅是临时文件)
docker image prune -f(强制删除镜像文件，无需弹出提示确认)
````

#### 复制文件到docker tomcat

````
docker cp /home/chuangye.war 2cbcb1fa0bf0:/usr/local/tomcat/webapps

docker cp /home/henansheng.json 2cbcb1fa0bf0:/usr/local/file  

````

#### 启动jenkins

````
docker run -d -p 13917:8080 -p 13918:50000 -v /home/jenkins_mount:/var/jenkins_home -v  /root/apache-maven-3.6.3:/usr/local/maven    -v /etc/localtime:/etc/localtime --name myjenkins jenkinsci/blueocean

docker run -d  --name jk -u root -p 13918:8080  -v /var/jenkins_home:/var/jenkins_home  jenkinsci/blueocean
````

````
docker run --name jenkins-blueocean -d -p 13918:8080 -p 13919:50000 -v jenkins_home:/var/jenkins_home jenkinsci/blueocean
````

#### jenkins汉化

````
1. 安装locale plugin，Localization:Chinese(Simplified)这两个插件。
2. 系统管理–>系统设置–>Locale 填入：zh_CN
````



## elasticsearch

**matser**

````
docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" \
--privileged=true --restart=always \
-d -p 13918:9200 \
-p 13919:9300 \
-v /home/docker_data/elasticsearch/config/master.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /home/docker_data/elasticsearch/master:/usr/share/elasticsearch/data --name es-master \ elasticsearch:latest
````

**slave**

````
 docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d \
 --privileged=true --restart=always \
  -p 13920:9201 \
  -p 13921:9301 \
 -v /home/docker_data/elasticsearch/config/slave.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
 -v /home/docker_data/elasticsearch/slave:/usr/share/elasticsearch/data \
 --name es-slave elasticsearch:latest

````



