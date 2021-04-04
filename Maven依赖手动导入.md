---
title: maven依赖手动导入
date: 2020-05-20 12:51:02
tags: 
---

暂无说明

<!-- more -->

#### 1. 到https://mvnrepository.com/下载jar包

#### 2. 修改导入命令

![](maven依赖手动导入/参数对照.png)



![](maven依赖手动导入/jar包存放位置.png)

F:\spring-boot-admin-starter-client-2.2.3.jar 是你下载后存放的jar包位置

#### 3. 最终命令

````
mvn install:install-file -DgroupId=de.codecentric -DartifactId=spring-boot-admin-starter-client -Dversion=2.2.3 -Dpackaging=jar -Dfile=F:\spring-boot-admin-starter-client-2.2.3.jar
````



````
mvn install:install-file -DgroupId=mysql -DartifactId=mysql-connector-java -Dversion=5.1.22 -Dpackaging=jar -Dfile=E:\mysql-connector-java-5.1.22.jar
````



