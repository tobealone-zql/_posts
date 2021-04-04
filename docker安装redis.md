---
title: Docker安装Redis
date: 2020-12-03 10:30:00
description: # 描述
tags: 
  - Docker
---

暂无说明

<!-- more -->

#### 拉取镜像

````
docker pull redis
````

#### 新建`data`和`conf`两个文件夹

````
mkdir -p /home/docker_data/redis/data
mkdir -p /home/docker_data/redis/conf
````

#### 增加配置文件 `redis.conf`

````
# 在刚才新建的redis/conf中新建文件redis.conf 内容如下：
#bind 127.0.0.1 
protected-mode no 
appendonly yes 
````

#### 创建`redis`容器并启动

````
docker run --name redis -p 13913:6379 --privileged=true  -v /home/docker_data/redis/data:/data -v /home/docker_data/redis/conf/redis.conf:/etc/redis/redis.conf -d redis redis-server /etc/redis/redis.conf
````

#### 参考教程

- https://blog.csdn.net/u010358168/article/details/97143703

