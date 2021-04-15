---
title: CentOS7初始化及基本操作
description: # 描述
tags: 
  - Centos
---

暂无说明

<!-- more -->

## 改为静态IP

````
# 编辑网卡配置文件
vi /etc/sysconfig/network-scripts/ifcfg-ens33
````

````
# 修改相关配置
BOOTPROTO="static" #dhcp改为static 
ONBOOT="yes" #开机启用本配置
IPADDR=192.168.7.106 #静态IP
GATEWAY=192.168.7.1 #默认网关
NETMASK=255.255.255.0 #子网掩码
DNS1=192.168.7.1 #DNS 配置
````

````
# 重启网络服务
service network restart
````

````
# 查看IP
ip addr
````

## yum安装wget

````
yum -y install wget
````



## 更改yum镜像源

````
 # 备份原来的配置
 mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
 # 下载ailiyun的yum源配置文件到/etc/yum.repos.d/
 wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
 # 运行yum makecache生成缓存
 yum makecache
 yum -y update
````



