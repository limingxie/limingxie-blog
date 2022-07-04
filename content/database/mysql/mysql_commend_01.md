---
author: "li_mingxie"
title: "【mysql笔记】mysql不常用，但是有用的命令"
date: 2022-07-06T12:28:49+08:00
tags: [
    "mysql",
    "show",
    "set",
]
categories: [
    "mysql",
    "database",
]
---

这篇简单的整理了开发过程中我们会用到的mysql的一些命令。  <!--more-->
其实不太懂也不会影响你开发，了解到了能提高一丢丢的效率...^^  

## 1.show 命令

```sql
#查看数据库
show databases;
show tablse;

#查看创建语句
show create database test
show create tatble test

#查看全局和回话变量
show global variables
show session variables

#默认查看会话变量，后面可以加like查询
show variables like '%version%'
show variables like '%character_%'
show variables like '%collation_%'
#执行计划缓存，命中低，所以只有5.7版本，8.0已经废弃
show variables like '%query_cache_type%'
```

## 2.set 命令

一般修改系统变量或会话变量。

```sql
#关掉自动提交
set autocommit=false;

#修改最大链接数
set global max_connections = 100
... ...
```

#### profiling

查看执行计划的时候使用，默认是关闭  

```sql
mysql> show variables like 'profiling%';
+------------------------+-------+
| Variable_name          | Value |
+------------------------+-------+
| profiling              | OFF   |
| profiling_history_size | 15    |
+------------------------+-------+
```

```sql
#开启
mysql> set profiling=1;
Query OK, 0 rows affected, 1 warning (0.00 sec)


mysql> select * from aa_innodb;
+----+------+
| id | name |
+----+------+
|  1 | aa1  |
|  2 | aa2  |
+----+------+
... ...

mysql> show profiles;
+----------+------------+----------------------------------+
| Query_ID | Duration   | Query                            |
+----------+------------+----------------------------------+
|        1 | 0.00765200 | select * from aa_innodb          |
|        2 | 0.00188275 | show variables like 'profiling%' |
|        3 | 0.00055175 | select * from aa_innodb          |
|        4 | 0.00041675 | select * from aa_innodb          |
|        5 | 0.00055025 | show profiling                   |
|        6 | 0.00018325 | show profilings                  |
|        7 | 0.00060450 | select * from aa_innodb          |
|        8 | 0.00043075 | select * from aa_innodb          |
+----------+------------+----------------------------------+

mysql> show profile for query 7;
+----------------------+----------+
| Status               | Duration |
+----------------------+----------+
| starting             | 0.000246 |
| checking permissions | 0.000021 |
| Opening tables       | 0.000035 |
| init                 | 0.000021 |
| System lock          | 0.000021 |
| optimizing           | 0.000014 |
| statistics           | 0.000031 |
| preparing            | 0.000022 |
| executing            | 0.000013 |
| Sending data         | 0.000046 |
| end                  | 0.000014 |
| query end            | 0.000014 |
| closing tables       | 0.000012 |
| freeing items        | 0.000052 |
| cleaning up          | 0.000046 |
+----------------------+----------+
```

#### my.cnf

这些配置项还可以修改 my.cnf文件。(set命令是一次性的修改，my.cnf是永久性的修改)  
然后需要重启数据库。如果生产环境中不宜重启的话，可以使用命令 + 修改 my.cnf的方式。  

## 3.desc 命令

当我们使用终端链接数据库时，用desc命令会很方便的查看查看表结构。  

```sql
mysql> desc user;
+-----------------------------+---------------------+------+-----+-------------------+-------+
| Field                       | Type                | Null | Key | Default           | Extra |
+-----------------------------+---------------------+------+-----+-------------------+-------+
| id                          | varchar(36)         | NO   | PRI | NULL              |       |
| email                       | varchar(100)        | YES  | MUL | NULL              |       |
| mobile                      | varchar(50)         | YES  |     | NULL              |       |
| login_name                  | varchar(100)        | NO   |     | NULL              |       |
... ...
| create_date                 | datetime            | NO   |     | CURRENT_TIMESTAMP |       |
| last_update                 | timestamp           | NO   |     | CURRENT_TIMESTAMP |       |
+-----------------------------+---------------------+------+-----+-------------------+-------+
```

## 4.\G

使用终端查询的时，字段或数据太多可以查询语句后面加  \G 可以查看到查询的结果。

```sql
mysql> select * from user limit 10 \G
*************************** 1. row ***************************
                         id: 03a4f5c0-9544-49bd-9dd4-a306dd247bf0
                      email: 18612967963@163.com
                     mobile: 18612967963
                 login_name: 18612967963
                        ... ...
                create_date: 2022-03-22 20:05:54
                last_update: 2022-05-12 14:24:07
*************************** 2. row ***************************
                         id: 03a4f5c0-9544-49bd-9dd4-a306dd247bf0
                        ... ...

```

## 小结

show 命令可以查看系统(session)参数。  
set 可以设置系统(session)参数。  
desc 可以查看表结构。  
\G 可以换一种方式查看查询的结果。  

下一遍整理了explain相关的命令。

----------------------------------------------

## PS

8.0版本新增了新的密码验证的方式。  
所以就得客户端链接报错的时候可以把密码的验证改回5.7的方式。  

```sql
use mysql;

#修改永红密码登录的规则
ALTER USER `root`@`localhost` IDENTIFIED WITH mysql_native_password BY '123456';

#刷新权限
FLUSH PRIVILEGES;
```

**执行脚本**

```sql
source ./xxx.sql;
```

**数据缓冲池**  
**innodb**:innodb_buffer_pool_size  
**myisam**:key_buffer_size  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
