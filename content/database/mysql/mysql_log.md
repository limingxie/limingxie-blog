---
author: "li_mingxie"
title: "【mysql笔记】redo和undo日志"
date: 2022-07-09T23:28:49+08:00
tags: [
    "mysql",
    "explain",
]
categories: [
    "mysql",
    "database",
]
---

这篇整理了redo log 和 undo log 相关的内容。   <!--more-->  

**REDO LOG**称为重做日志，  
提供再写入操作，恢复提交事务修改的页操作，用来保证事务的持久性。(存储物理数据)  

**UNDO LOG**称为回滚日志，  
回滚行记录到某个特定版本，用来保证事务的原子性、一致性。(存储逻辑数据)  

## 1.redo log

#### 1.1 特点  

redo日志是顺序写入磁盘的  
事务执行过程中，redo log不断记录  

#### 1.2 重做日志的缓冲 (redo log buffer)

redo log buffer 大小默认 16M ，最大值是4096M，最小值为1M。

```sql
mysql> show variables like '%innodb_log_buffer_size%';
+------------------------+----------+
| Variable_name          | Value    |
+------------------------+----------+
| innodb_log_buffer_size | 16777216 |
+------------------------+----------+
```

#### 1.3 redo的整体流程

[图片备用地址](https://limingxie.github.io/images/database/mysql/redo_log1.png)  
![mysql_redo_log](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/redo_log1.png?x-oss-process=image/resize,w_700,m_lfit)

```bash
第1步:先将原始数据从磁盘中读入内存中来，修改数据的内存拷贝
第2步:生成一条重做日志并写入redo log buffer，记录的是数据被修改后的值 
第3步:当事务commit时，将redo log buffer中的内容刷新到 redo log file，对 redo log file采用追加 写的方式
第4步:定期将内存中修改的数据刷新到磁盘中
```

#### 1.4 innodb_flush_log_at_trx_commit 参数

**设置为0** :  
表示每次事务提交时不进行刷盘操作。(系统默认masterthread每隔1s进行一次重做日 志的同步)  
[图片备用地址](https://limingxie.github.io/images/database/mysql/redo_log2.png)  
![mysql_redo_log](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/redo_log2.png?x-oss-process=image/resize,w_700,m_lfit)

**设置为1** :  
表示每次事务提交时都将进行同步，刷盘操作(默认值)  

[图片备用地址](https://limingxie.github.io/images/database/mysql/redo_log3.png)  
![mysql_redo_log](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/redo_log3.png?x-oss-process=image/resize,w_700,m_lfit)

**设置为2** :  
表示每次事务提交时都只把 redo log buffer 内容写入 page cache，不进行同步。由os自己决定什么时候同步到磁盘文件。  

[图片备用地址](https://limingxie.github.io/images/database/mysql/redo_log4.png)  
![mysql_redo_log](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/redo_log4.png?x-oss-process=image/resize,w_700,m_lfit)

#### 1.5 redo log buffer

有点偏底层，没有细研究 => 略掉

#### 1.6 redo log file相关参数

**innodb_log_group_home_dir:**  
指定redo log文件组所在的路径，默认值为 ./ ，表示在数据库 的数据目录下。  
MySQL的默认数据目录(var/lib/mysql)下默认有两个名为ib_logfile0和ib_logfile1的文件，log buffer中的日志默认情况下就是刷新到这两个磁盘文件中。  
此redo日志 文件位置还可以修改。

**innodb_log_files_in_group:**  
指明redo log file的个数，命名方式如:ib_logfile0，iblogfile1... iblogfilen。默认2个，最大100个。  

**innodb_flush_log_at_trx_commit:**  
控制 redo log 刷新到磁盘的策略，默认为1。  

**innodb_log_file_size:**  
单个 redo log 文件设置大小，默认值为 48M 。最大值为512G，注意最大值 指的是整个 redo log 系列文件之和，  
即(innodb_log_files_in_group * innodb_log_file_size )不能大 于最大值512G。

```sql
mysql> show variables like 'innodb_log_files_in_group';
+---------------------------+-------+
| Variable_name             | Value |
+---------------------------+-------+
| innodb_log_files_in_group | 2     |
+---------------------------+-------+

mysql> show variables like 'innodb_log_file_size';
+----------------------+----------+
| Variable_name        | Value    |
+----------------------+----------+
| innodb_log_file_size | 50331648 |
+----------------------+----------+
```

## 2.undo log

redo og是事务持久性的保证，undo log是事务原子性的保证。  
在事务中更新数据的前置操作其实是要先写入一个undo log。

#### 2.1 Undo日志的作用

作用1:回滚数据
作用2:MVCC

#### 2.2 undo 结构

**回滚段**  
InnoDB对undo log的管理采用段的方式，也就是回滚段(rollback segment)。  
每个回滚段记录了 1024个undo log segment，而在每个undologsegment段中进行undo页的申请。  

**undo页**  
从1.1版本开始InnoDB支持最大128个rollback segment ，故其支持同时在线的事务限制提高到了128*1024 。

```sql
mysql> show variables like 'innodb_undo_logs';
+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| innodb_undo_logs | 128   |
+------------------+-------+
```

#### 2.3 回滚段与事务

1. 每个事务只会使用一个回滚段，一个回滚段在同一时刻可能会服务于多个事务。  
2. 当一个事务开始的时候，会制定一个回滚段，在事务进行的过程中，当数据被修改时，原始的数 据会被复制到回滚段。  
3. 在回滚段中，事务会不断填充盘区，直到事务结束或所有的空间被用完。如果当前的盘区不够用，事务会在段中请求扩展下一个盘区，如果所有已分配的盘区都被用完，事务会覆盖最初的盘 区或者在回滚段允许的情况下扩展新的盘区来使用。  
4. 回滚段存在于undo表空间中，在数据库中可以存在多个undo表空间，但同一时刻只能使用一个undo表空间。  
5. 当事务提交时，InnoDB存储引擎会做以下两件事情:  
    1）将undo log放入列表中，以供之后的purge操作  
    2）判断undo log所在的页是否可以重用，若可以分配给下个事务使用

#### 2.4 回滚段中的数据分类

1. 未提交的回滚数据(uncommitted undo information)
2. 已经提交但未过期的回滚数据(committed undo information)
3. 事务已经提交并过期的数据(expired undo information)

#### 2.5 undo的类型

在InnoDB存储引擎中，undo log分为:  
insert undo log  
update undo log  

## 3.小结

* undo log是逻辑日志，对事务回滚时，只是将数据库逻辑地恢复到原来的样子。  
* redo log是物理日志，记录的是数据页的物理变化，undo log不是redo log的逆过程。  

[图片备用地址](https://limingxie.github.io/images/database/mysql/redo_undo_log.png)  
![mysql_redo_log](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/database/mysql/redo_undo_log.png?x-oss-process=image/resize,w_700,m_lfit)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
