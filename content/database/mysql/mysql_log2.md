---
author: "li_mingxie"
title: "【mysql笔记】mysql日志"
date: 2022-07-10T23:28:49+08:00
tags: [
    "mysql",
    "explain",
]
categories: [
    "mysql",
    "database",
]
---

上一篇文章介绍了redo log和undo log。  
这里整齐除了这两个以外的mysql的日志。  <!--more-->  

## 1.日志类型

#### 5.7的4中类型  

**慢查询日志Slow query log:**  
记录所有执行时间超过long_query_time的所有查询，方便我们对查询进行优化。  

**通用查询日志General query log:**  
记录所有连接的起始时间和终止时间，以及连接发送给数据库服务器的所有指令，  
对我们复原操作的实际场景、发现问题，甚至是对数据库操作的审计都有很大的帮助。  

**错误日志Error log:**  
记录MySQL服务的启动、运行或停止MySQL服务时出现的问题，方便我们了解服务器的 状态，从而对服务器进行维护。

**二进制日志Binary log:**  
记录所有更改数据的语句，可以用于主从服务器之间的数据同步，以及服务器遇到故 障时数据的无损失恢复。

#### 8.0新增的2种类型  

**中继日志Relay log**: 用于主从服务器架构中，从服务器用来存放主服务器二进制日志内容的一个中间文件。  
从服务器通过读取中继日志的内容，来同步主服务器上的操作。
  
**数据定义语句日志**: 记录数据定义语句执行的元数据操作  

除二进制日志外，其他日志都是 文本文件 。默认情况下，所有日志创建于 MySQL数据目录中。  

## 2.慢查询日志(slow query log)

```sql
#开启慢查询日志
show variables like '%slow_query_log%'
set global slow_query_log='ON'
set global slow_query_log_file='/tmp/mysql_slow.log'
```

```sql
#设置慢查询设定
set global long_query_time = 1;
show global variables like '%long_query_time%';
set long_query_time=1;
show variables like '%long_query_time%';
```

```sql
#慢查询出现多少次，做记录。100代表出现100次了 才做记录
show variables like '%min_examined_row_limit%'

#查看慢查询
SHOW GLOBAL STATUS LIKE '%Slow_queries%'
show status like 'slow_queries'
```

## 3.通用查询日志(general query log)

#### 3.1 查看状态

```sql
mysql> SHOW VARIABLES LIKE '%general%'; 
+------------------+------------------------------+
| Variable_name    | Value                        | 
+------------------+------------------------------+
| general_log      | OFF                          | #通用查询日志处于关闭状态
| general_log_file | /var/lib/mysql/atguigu01.log | #通用查询日志文件的名称是atguigu01.log 
+------------------+------------------------------+
```

#### 3.2 启动日志

**永久启动：**修改my.cnf或者my.ini配置文件来设置。

```
[mysqld]
general_log=ON  /  OFF
general_log_file=[path[filename]] #日志文件所在目录路径，filename为日志文件名
```

**临时启动：**  

```sql
SET GLOBAL general_log=on; # 开启通用查询日志
SET GLOBAL general_log_file=’path/filename’; # 设置日志文件保存位置 
SET GLOBAL general_log=off; # 关闭通用查询日志 
SHOW VARIABLES LIKE 'general_log%';
```

#### 3.3 查看日志

```
SHOW VARIABLES LIKE 'general_log%'; 
```

使用如下命令重新生成查询日志文件，具体命令如下。  
刷新MySQL数据目录，发现创建了新的日志文 件。前提一定要开启通用日志。  

```
mysqladmin -uroot -p flush-logs
```

## 4.错误日志(error log)

在MySQL数据库中，错误日志功能是默认开启的。而且，错误日志无法被禁止。  

**永久设定日志路径**  

```
[mysqld]
log-error=[path/[filename]] #path为日志文件所在的目录路径，filename为日志文件名
```

**查看错误日志信配置息**  

```sql
mysql> SHOW VARIABLES LIKE 'log_err%';
+----------------------------+----------------------------------------+
| Variable_name              | Value                                  |
+----------------------------+----------------------------------------+
| log_error                  | /var/log/mysqld.log                    |
| log_error_services         | log_filter_internal; log_sink_internal |
| log_error_suppression_list |                                        |
| log_error_verbosity        | 2                                      |
+----------------------------+----------------------------------------+
```

**删除日志**  

```
[root@atguigu01 log]# mysqladmin -uroot -p flush-logs
Enter password:
mysqladmin: refresh failed; error: 'Could not open file '/var/log/mysqld.log' for
error logging.'
```

**需要做补充操作**  

```
install -omysql -gmysql -m0644 /dev/null /var/log/mysqld.log
```

## 5. 二进制日志(bin log)

#### 5.1 查看信息

```
mysql> show variables like '%log_bin%';
+---------------------------------+----------------------------------+
| Variable_name                   | Value                            |
+---------------------------------+----------------------------------+
| log_bin                         | ON                               |
| log_bin_basename                | /var/lib/mysql/binlog            |
| log_bin_index                   | /var/lib/mysql/binlog.index      |
| log_bin_trust_function_creators | OFF                              |
| log_bin_use_v1_row_events       | OFF                              |
| sql_log_bin                     | ON                               |
+---------------------------------+----------------------------------+
```

#### 5.2 参数设置

**永久**

```
[mysqld]
# 启用二进制日志
log-bin=test-bin
binlog_expire_logs_seconds=600
max_binlog_size=100M
```

```
[mysqld]
log-bin="/var/lib/mysql/binlog/test-bin"
```

注意:新建的文件夹需要使用mysql用户，使用下面的命令即可。  

```
chown -R -v mysql:mysql binlog
```

**临时**

```sql
# global 级别
mysql> set global sql_log_bin=0;
ERROR 1228 (HY000): Variable 'sql_log_bin' is a SESSION variable and can`t be used with SET GLOBAL
# session级别
mysql> SET sql_log_bin=0;
Query OK, 0 rows affected (0.01 秒)
```

#### 5.3 查看日志

```sql
mysql> SHOW BINARY LOGS; 
+--------------------+-----------+-----------+ 
| Log_name           | File_size | Encrypted | 
+--------------------+-----------+-----------+ 
| test-bin.000001    | 156       | No        | 
+--------------------+-----------+-----------+
```

```sql
mysqlbinlog -v "/var/lib/mysql/binlog/test-bin.000002"

mysqlbinlog -v --base64-output=DECODE-ROWS "/var/lib/mysql/binlog/test-bin.000002"

# 可查看参数帮助
mysqlbinlog --no-defaults --help
# 查看最后100行
mysqlbinlog --no-defaults --base64-output=decode-rows -vv test-bin.000002 |tail -100
# 根据position查找
mysqlbinlog --no-defaults --base64-output=decode-rows -vv test-bin.000002 |grep -A 20 '4939002'
```

```sql
mysql> show binlog events [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count];
# IN 'log_name':指定要查询的binlog文件名(不指定就是第一个binlog文件)  
# FROM pos:指定从哪个pos起始点开始查起(不指定就是从整个文件首个pos点开始算)
# LIMIT [offset]:偏移量(不指定就是0) 
# row_count:查询总条数(不指定就是所有行)

mysql> show binlog events in 'test-bin.000002';
```

#### 5.4 使用日志恢复数据

mysqlbinlog恢复数据的语法如下:

```
mysqlbinlog [option] filename|mysql –uuser -ppass;
```

#### 5.5 删除二进制日志

PURGE MASTER LOGS:删除指定日志文件

```
PURGE {MASTER | BINARY} LOGS TO ‘指定日志文件名’ 
PURGE {MASTER | BINARY} LOGS BEFORE ‘指定日期’
```

#### 5.6 binlog与redolog对比

redolog 是物理日志，记录内容是“在某个数据页上做了什么修改”，属于 InnoDB 存储引擎层产生的。
binlog 是逻辑日志，记录内容是语句的原始逻辑，类似于“给 ID=2 这一行的 c 字段加 1”，属于MySQL Server 层。

#### 5.7 sync_binlog

0 => 表示每次提交事务都只 write，由系统自行判断什么时候执行fsync。
1 => 表示每次提交事务都会执行fsync，就如同redo log 刷盘流程一样。  
N(N>1)，表示每次提交事务都write，但累积N个事务后才file sync。

[图片备用地址](https://limingxie.github.io/images/database/mysql/binlog.png)  
![mysql_binlog](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/binlog.png?x-oss-process=image/resize,w_700,m_lfit)

## 6.中继日志(relay log)

中继日志只在主从服务器架构的从服务器上存在 。

[图片备用地址](https://limingxie.github.io/images/database/mysql/relay_log.png)  
![mysql_relay_log](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/relay_log.png?x-oss-process=image/resize,w_700,m_lfit)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
