---
title: OpenWrt主路由基本设置
date: 2021-02-13 11:36:00
tags:
---

暂无说明

<!--more-->

## 说明

- 本教程基于eSir的 OpenWrt R21.1.18 GDQ v2.1[2021]版本

## 预实现功能（暂作记录，未能实现）

- 拨号

- 科学上网
- 负载均衡
- 通过动态DNS插件解析OpenWrt主机所分配的IPV6，绑定域名。实现外网访问OpenWrt后台
- 通过动态DNS插件解析网内主机所分配的IPV6，绑定域名。实现外网控制电脑主机

## 网络拓扑图

![image-20210213184305307](OpenWrt主路由基本设置/image-20210213184305307.png)





##  拨号，并对接口进行基本配置（以下每个页面修改完都要点击保存应用，等到最后再点击，前面修改的配置就没了）

1. 点击 左侧导航栏 网络 - 接口，删除除了LAN口和WAN口之外的接口

   ![image-20210213201625528](OpenWrt主路由基本设置/image-20210213201625528.png)

2. 点击WAN口修改按钮

   ![image-20210213201745239](OpenWrt主路由基本设置/image-20210213201745239.png)

3. WAN口：基本配置改为如下设置，PAP/CHAP 用户名，密码改为自己的宽带账号密码

![image-20210213202231140](OpenWrt主路由基本设置/image-20210213202231140.png)

4. WAN口：高级设置改为如下设置

   ![image-20210213202438292](OpenWrt主路由基本设置/image-20210213202438292.png)

5. WAN口：物理设置改为如下设置，因为我的连接光猫的网线是连接在软路由的eth0口的。

   ![image-20210213202803464](OpenWrt主路由基本设置/image-20210213202803464.png)

6. WAN口：防火墙设置改为如下设置

![image-20210213202945238](OpenWrt主路由基本设置/image-20210213202945238.png)

7. 点击保存&应用。并返回接口页面

![image-20210213203140282](OpenWrt主路由基本设置/image-20210213203140282.png)

8. 点击LAN口修改按钮

![image-20210213203304066](OpenWrt主路由基本设置/image-20210213203304066.png)

9. LAN口：基本设置，以及DHCP 服务器基本设置改为如下

![image-20210213203732334](OpenWrt主路由基本设置/image-20210213203732334.png)

10. LAN口：DHCP 服务器高级设置改为如下

![image-20210213203558722](OpenWrt主路由基本设置/image-20210213203558722.png)

11. LAN口：DHCP 服务器IPV6设置改为如下

![image-20210213203857231](OpenWrt主路由基本设置/image-20210213203857231.png)

12. LAN口：高级配置改为如下

![image-20210213204015391](OpenWrt主路由基本设置/image-20210213204015391.png)

13. LAN口：物理设置改为如下

![image-20210213204103729](OpenWrt主路由基本设置/image-20210213204103729.png)

14. LAN口：防火墙设置改为如下

![image-20210213204218445](OpenWrt主路由基本设置/image-20210213204218445.png)

15. 点击保存& 应用，并点击接口，返回接口管理界面，查看信息

![image-20210213204429554](OpenWrt主路由基本设置/image-20210213204429554.png)



## 科学上网

1. 点击 服务 - shadowSocksR Plus+

![image-20210213210211443](OpenWrt主路由基本设置/image-20210213210211443.png)

2. 做如下配置

   ![image-20210213210510736](OpenWrt主路由基本设置/image-20210213210510736.png)

   3. 选一个状态好的节点，点击应用

   ![image-20210213210820407](OpenWrt主路由基本设置/image-20210213210820407.png)

   4. 结果如下

   ![image-20210213210845470](OpenWrt主路由基本设置/image-20210213210845470.png)

## 配置负载均衡

1. 点击 网络 - 负载均衡

![image-20210213204546068](OpenWrt主路由基本设置/image-20210213204546068.png)

2. 点击修改

![image-20210213204634126](OpenWrt主路由基本设置/image-20210213204634126.png)

3. 点击策略，点击修改

![image-20210213204822723](OpenWrt主路由基本设置/image-20210213204822723.png)

4. 备用成员改为默认（使用主路由表），保存应用

![image-20210213204923549](OpenWrt主路由基本设置/image-20210213204923549.png)

5. 结果如下

![image-20210213205551262](OpenWrt主路由基本设置/image-20210213205551262.png)

6. 点击规则，点击修改

![image-20210213205054633](OpenWrt主路由基本设置/image-20210213205054633.png)

7. 分配的策略改为默认（使用主路由表），保存应用，返回上一级

![image-20210213205140917](OpenWrt主路由基本设置/image-20210213205140917.png)

8. 点击修改

![image-20210213205246760](OpenWrt主路由基本设置/image-20210213205246760.png)

8. 分配的策略改为默认（使用主路由表），保存应用

![image-20210213205309462](OpenWrt主路由基本设置/image-20210213205309462.png)

9. 结果如下

   ![image-20210213205411379](OpenWrt主路由基本设置/image-20210213205411379.png)



## 配置Turbo ACC网络加速

1. 点击 网络 - Turbo ACC网络加速

![image-20210213205908299](OpenWrt主路由基本设置/image-20210213205908299.png)

2. 修改如下配置

![image-20210213210013806](OpenWrt主路由基本设置/image-20210213210013806.png)

3. 结果如下

![image-20210213210052524](OpenWrt主路由基本设置/image-20210213210052524.png)

## 配置动态DNS，实现外网访问该系统（具体域名购买，另行百度）

1. 点击动态DNS插件。删除原有配置

![image-20210213211415625](OpenWrt主路由基本设置/image-20210213211415625.png)

2. 自定义名称，点击添加

![image-20210213211522200](OpenWrt主路由基本设置/image-20210213211522200.png)

2. 基本配置如下

![image-20210213211954679](OpenWrt主路由基本设置/image-20210213211954679.png)

3. 高级设置如下

![image-20210213212137577](OpenWrt主路由基本设置/image-20210213212137577.png)

4. 保存应用。点击启动

![image-20210213212225740](OpenWrt主路由基本设置/image-20210213212225740.png)

5. 结果如下

![image-20210213212304485](OpenWrt主路由基本设置/image-20210213212304485.png)





