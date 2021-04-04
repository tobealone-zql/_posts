---

title: Centos操作命令
date: 2020-05-07 23:04:47
description: # 描述
tags: 
  - Centos
---

暂无说明

<!-- more -->

#### 重启 

````
shutdown -r now
````

- 查看Mysql是否启动(显示PID为启动，不显示为未启动)

```` 
pidof mysqld
````

#### 修改网卡

````
vi /etc/sysconfig/network-scripts/ifcfg-ens33
````

#### 网卡重启

````
service network restart
````

#### 解压

````
tar -zxvf apache-tomcat-9.0.40.tar.gz
````

#### 防火墙

````
# 关闭防火墙
systemctl stop firewalld.service
# 暴露端口
# firewall-cmd --permanent --zone=public --add-port=2048/tcp
````

````
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"

DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="enp4s0"
UUID="21e599b5-84ab-4a5d-a6f4-18a5adbc4060"
DEVICE="enp4s0"
ONBOOT="yes"

BOOTPROTO="static"
IPADDR=192.168.22.112 #静态IP
GATEWAY=192.168.22.1 #默认网关
NETMASK=255.255.255.0 #子网掩码
DNS1=114.114.114.114 #DNS 配置
DNS2=8.8.8.8 #DNS 配置

````

#### 关机

````
shutdown -h now
````

#### 防火墙管理

````
systemctl enable firewalld
systemctl start firewalld
systemctl status firewalld
# 开放端口
firewall-cmd --add-port=666/tcp --permanent
# 重新载入配置
firewall-cmd --reload
````

#### 删除文件夹

````
rm -rf 文件夹名
````

#### 查看程序启动状态

````
ps -ef|grep tomcat
````



## FastDFS

#### 重新载入配置
 启动启动tracker服务

````
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
````

#### 重新载入配置
 启动storage

````
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
````

## Redis

#### 重新载入配置
 启动

````
redis-server.exe redis.windows.conf
````

#### 重新载入配置
 重启

````
systemctl restart redis
````



## Sentinel

#### 启动

````
java -Dserver.port=8718 -Dcsp.sentinel.dashboard.server=localhost:8718 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
````

````
update user set password=password('root') where user='root';
````

````
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
````

## Mysql

#### 重启

````
service mysqld restart
````

````
GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'192.168.10.%' IDENTIFIED BY 'repl_user';
FLUSH PRIVILEGES;
````

#### 设置简单密码

````
set global validate_password_policy=0;
set global validate_password_length=1;
````

## Tomcat

#### 实时查看日志

````
tail -f /usr/javaweb/application/apache-tomcat-9.0.39/logs/catalina.out
````









