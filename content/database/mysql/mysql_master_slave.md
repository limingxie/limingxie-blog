---
author: "li_mingxie"
title: "【mysql笔记】主从复制"
date: 2022-07-11T23:28:49+08:00
tags: [
    "mysql",
    "explain",
]
categories: [
    "mysql",
    "database",
]
---

这篇整理了mysql主从复制相关的内容。   <!--more-->  

## 1.主从复制的作用

1. 读写分离
2. 数据备份
3. 高可用性

## 2.主从复制的原理

#### 2.1 复制的基本原则

1. 每个Slave只有一个 Master
2. 每个Slave只能有一个唯一的服务器ID
3. 每个Master可以有多个 Slave

#### 2.2 三个线程

实际上主从同步的原理就是基于binlog 进行数据同步的。在主从复制过程中，会基于**3个线程**来操作，一个主库线程，两个从库线程。  
**二进制日志转储线程 (Binlog dump thread)**是一个主库线程。当从库线程连接的时候，
主库可以将二进 制日志发送给从库，当主库读取事件(Event)的时候，会在 Binlog 上 加锁 ，读取完成之后，再将锁释 放掉。  
**从库 I/O线程**会连接到主库，向主库发送请求更新Binlog。  
这时从库的I/O线程就可以读取到主库的 二进制日志转储线程发送的 Binlog 更新部分，并且拷贝到本地的中继日志 (Relay log)。
**从库SQL线程**会读取从库中的中继日志，并且执行日志中的事件，将从库中的数据与主库保持同步。

[图片备用地址](https://limingxie.github.io/images/database/mysql/master_slave2.png)  
![mysql_master_slave](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/master_slave2.png?x-oss-process=image/resize,w_700,m_lfit)

#### 2.3 复制三步骤

步骤1: Master 将写操作记录到二进制日志( binlog )。
步骤2: Slave 将 Master 的binary log events拷贝到它的中继日志( relay log );
步骤3: Slave 重做中继日志中的事件，将改变应用到自己的数据库中。 MySQL复制是异步的且串行化 的，而且重启后从 接入点 开始复制

[图片备用地址](https://limingxie.github.io/images/database/mysql/master_slave1.png)  
![mysql_master_slave](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/master_slave1.png?x-oss-process=image/resize,w_700,m_lfit)

## 3.搭建主从复制架构

#### 3.1 准备工作

**需要注意的地方**  
1 MAC地址 2 hostname 3 IP 地址 4 UUID 。  

查看MySQL Server 的UUID方式

```bash
vim /var/lib/mysql/auto.cnf
systemctl restart mysqld
```

#### 3.2 主机配置

主从所有配置项都配置在[mysqld]节点下，且都是小写字母。  
具体参数配置如下:

**my.cnf**  

```
#[必须]主服务器唯一ID 
server-id=1
#[必须]启用二进制日志,指名路径。比如:自己本地的路径/log/mysqlbin 
log-bin=atguigu-bin

#[可选] 0(默认)表示读写(主机)，1表示只读(从机) 
read-only=0

#设置日志文件保留的时长，单位是秒 
binlog_expire_logs_seconds=6000

#控制单个二进制日志大小。此参数的最大和默认值是1GB 
max_binlog_size=200M

#[可选]设置不要复制的数据库 
binlog-ignore-db=test

#[可选]设置需要复制的数据库,默认全部记录。比如:binlog-do-db=atguigu_master_slave 
binlog-do-db=需要复制的主数据库名字

#[可选]设置binlog格式 
binlog_format=STATEMENT
```

**binlog格式设置**  

```
#存的是具体操作SQL语句，
binlog_format=STATEMENT 

#存的是具体操作的数据
binlog_format=ROW

#混合上面2个
binlog_format=MIXED
```

#### 3.3 从机配置

```bash
#[必须]从服务器唯一ID 
server-id=2

#[可选]启用中继日志 
relay-log=mysql-relay
```

> 注意:主从机都关闭防火墙
> service iptables stop #CentOS 6
> systemctl stop firewalld.service #CentOS 7

#### 3.4 主机:建立账户并授权

**5.7 版本**  

```sql
#在主机MySQL里执行授权主从复制的命令
GRANT REPLICATION SLAVE ON *.* TO 'slave1'@'从机器数据库IP' IDENTIFIED BY 'abc123';
```

**8.0**  

```sql
CREATE USER 'slave1'@'%' IDENTIFIED BY '123456';

GRANT REPLICATION SLAVE ON *.* TO 'slave1'@'%';

#此语句必须执行。否则见下面。
ALTER USER 'slave1'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

flush privileges;
```

查询Master的状态，并记录下File和Position的值。

```sql
mysql> show master status;
+--------------------+----------+--------------+------------------+-------------------+
| File               | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+--------------------+----------+--------------+------------------+-------------------+
| mingxie-bin.000001 |    12339 | rdc_manager  |                  |                   |
+--------------------+----------+--------------+------------------+-------------------+
```

>注意:执行完此步骤后 不要再操作主服务器MySQL，防止主服务器状态值变化

#### 3.5 从机:配置需要复制的主机

**步骤1: 从机上复制主机的命令**  

```sql
CHANGE MASTER TO
MASTER_HOST='主机的IP地址', 
MASTER_USER='主机用户名', 
MASTER_PASSWORD='主机用户名的密码', 
MASTER_LOG_FILE='mysql-bin.具体数字', 
MASTER_LOG_POS=具体值;
```

举例:

```sql
CHANGE MASTER TO
MASTER_HOST='127.0.0.1',
MASTER_USER='slave1',
MASTER_PASSWORD='123456',
MASTER_LOG_FILE='mingxie-bin.000001',
MASTER_LOG_POS=12339;
```

**步骤2: 启动slave同步**  

```sql
#启动slave同步 
START SLAVE;
```

查看同步状态,如下两个值为Yes就说明配置成功。  

Slave_IO_Running: Yes  
Slave_SQL_Running: Yes  

```
SHOW SLAVE STATUS\G;

mysql> SHOW SLAVE STATUS\G;
*************************** 1. row ***************************
                            ... ...
                Relay_Log_Pos: 5726
        Relay_Master_Log_File: mingxie-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
                            ... ...
```

如出现问题需要重置的时候先执行这个命令。  

## 3.6 停止和重置

```sql
stop slave;

#删除SLAVE数据库的relaylog日志文件，并重新启用新的relaylog文件
mysql> reset slave;

#删除Master中所有的binglog文件，并将日志索引文件清空，重新开始所有新的日志文件(慎用)
reset master; 
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
