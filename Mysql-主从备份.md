---
title: Mysql-主从备份
date: 2021-02-24 10:03:21
tags: - Mysql
description:
---

暂无说明

<!-- more -->

## 环境

- Mysql版本：8.0
- Cenntos 7
- Mysql Master Host IP : 192.168.22.112:13911
- Mysql Slave Host IP : 192.168.22.112:13915
- MySQL同步账户：-u username -p root

## 配置

#### 1. 修改Master上MySQL配置文件my.cnf

````
[mysqld]
log-bin=mysql-bin   // [必须]启用二进制日志
server-id=1         // [必须]服务器唯一ID
````

#### 2. 修改Slave上MySQL配置文件my.cnf

````
[mysqld]
log-bin=mysql-bin   // [非必须]Slave可以不启用二进制日志，配置二进制日志可以便于Master和Slave交换角色
server-id=2         // [必须]服务器唯一ID
````

#### 3. 重启Master和Slave上的MySQL

#### 4. 在Master上使用root用户登录建立同步账户并授权Slave(命令与5.7版本不同)

````
mysql> CREATE USER 'root01'@'192.168.22.112' IDENTIFIED WITH mysql_native_password BY 'root01';
mysql> GRANT REPLICATION SLAVE ON *.* TO 'root01'@'192.168.22.112';
````

#### 5. 使用root账户登录Master查看Master状态

````
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000003 |    211196  |             |                  |                   |
+------------------+----------+--------------+------------------+-------------------+

````

#### 6. 配置Slave跟踪Master日志的位置

````
mysql> CHANGE MASTER TO
MASTER_HOST='192.168.22.112',
MASTER_USER='root01',
MASTER_PASSWORD='root01',
MASTER_LOG_FILE='mysql-bin.000003',
MASTER_PORT = 13911,
MASTER_LOG_POS=638864;
mysql> start slave;
````

#### 7. 使用root账户登录MySQL核对Slave状态

````
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 192.168.22.112
                  Master_User: root01
                  Master_Port: 13911
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000003
          Read_Master_Log_Pos: 638864
               Relay_Log_File: 5aaa26602511-relay-bin.000002
                Relay_Log_Pos: 324
        Relay_Master_Log_File: mysql-bin.000003
             Slave_IO_Running: Yes
            Slave_SQL_Running: No

````

#### 8. 注意点

- 对于需要备份的数据库数据，从库需要和主库完全一致，否则在初始化的时候会同步失败
- 如果两个Mysql都是通过Docker装在一个宿主机上，宿主机需要关闭防火墙，从数据库才能连接主数据库（原因暂时未知）























## 参考教程

- [MySQL主从备份配置](https://www.jianshu.com/p/1eed312e83bf)