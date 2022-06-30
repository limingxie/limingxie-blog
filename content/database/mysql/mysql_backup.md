---
author: "li_mingxie"
title: "【mysql笔记】数据库备份和还原"
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

这篇整理了备份还原相关的内容。   <!--more-->  

## 1. 物理备份与逻辑备份

#### 物理备份

备份数据文件，转储数据库物理文件到某一目录。(只支持MyISAm，简单了解略过)  
物理备份恢复速度比较快，但占用空间比较大，MySQL中可以用 xtrabackup 工具来进行物理备份。  

#### 逻辑备份

对数据库对象利用工具进行导出工作，汇总入备份文件内。  
逻辑备份恢复速度慢，但占用空 间小，更灵活。MySQL 中常用的逻辑备份工具为 mysqldump 。  
逻辑备份就是 备份sql语句 ，在恢复的 时候执行备份的sql语句实现数据库数据的重现。

## 2. mysqldump实现逻辑备份

#### 2.1 备份一个数据库

```
基本语法:  
mysqldump –u 用户名称 –h 主机名称 –p密码 待备份的数据库名称[tbname, [tbname...]]> 备份文件名 称.sql
```

```sql
mysqldump -uroot -p testdb>testdb.sql  #备份文件存储在当前目录下
mysqldump -uroot -p testdb > /var/lib/mysql/testdb.sql
```

#### 2.2 备份全部数据库

若想用mysqldump备份整个实例，可以使用 --all-databases 或 -A 参数:

```sql
mysqldump -uroot -pxxxxxx --all-databases > all_database.sql
mysqldump -uroot -pxxxxxx -A > all_database.sql
```

#### 2.3 备份部分数据库

使用 --databases 或 -B 参数

```
mysqldump –u user –h host –p --databases [数据库的名称1 [数据库的名称2...]] > 备份文件名 称.sql
```

```sql
mysqldump -uroot -p --databases testdb testdb11 >two_database.sql
mysqldump -uroot -p -B testdb testdb11 > two_database.sql
```

#### 2.4 备份部分表

```
mysqldump –u user –h host –p 数据库的名称 [表名1 [表名2...]] > 备份文件名称.sql
```

```sql
mysqldump -uroot -p testdb student> book.sql
```

#### 2.5 备份单表的部分数据

```sql
mysqldump -uroot -p testdb student --where="id < 10 " > student_part_id10_low_bak.sql
```

#### 2.6 排除某些表的备份

选项 --ignore-table 可以完成这个功能

```sql
mysqldump -uroot -p testdb --ignore-table=testdb.student > no_stu_bak.sql
grep "student" no_stu_bak.sql
```

#### 2.7 只备份结构或只备份数据

只备份结构的话可以使用 --no-data 简写为 -d 选项;  
只备份数据可以使用 --no-create-info 简写为-t 选项。

```sql
mysqldump -uroot -p testdb --no-data > testdb_no_data_bak.sql
# grep "INSERT" testdb_no_data_bak.sql
mysqldump -uroot -p testdb --no-create-info > testdb_no_create_info_bak.sql
# grep "CREATE" testdb_no_create_info_bak.sql
```

#### 2.8 备份中包含存储过程、函数、事件

mysqldump备份默认是不包含存储过程，自定义函数及事件的。  
可以使用 --routines 或 -R 选项来备份存储过程及函数，  
使用 --events 或 -E 参数来备份事件。

```sql
mysqldump -uroot -p -R -E --databases testdb > fun_testdb_bak.sql
```

## 3. mysql恢复数据

基本语法:

```sql
mysql –u root –p [dbname] < backup.sql
```

#### 3.1 单库备份中恢复单库

```sql
mysql -uroot -p < testdb.sql
mysql -uroot -p testdb< testdb.sql
```

#### 3.2 全量备份恢复

```sql
mysql –u root –p < all.sql
mysql -uroot -pxxxxxx < all.sql
```

#### 3.3 从全量备份中恢复单库

```sql
sed -n '/^-- Current Database: `testdb`/,/^-- Current Database: `/p' all_database.sql
> testdb.sql
#分离完成后我们再导入testdb.sql即可恢复单个库
```

#### 3.4 从单库备份中恢复单表

```sql
cat testdb.sql | sed -e '/./{H;$!d;}' -e 'x;/CREATE TABLE `class`/!d;q' > class_structure.sql
cat testdb.sql | grep --ignore-case 'insert into `class`' > class_data.sql 
#用shell语法分离出创建表的语句及插入数据的语句后 再依次导出即可完成恢复

use testdb;
mysql> source class_structure.sql;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> source class_data.sql;
Query OK, 1 row affected (0.01 sec)
```

## 4.导入导出文件

#### 4.1 导出文件

```sql
mysql> SHOW GLOBAL VARIABLES LIKE '%secure%';
+--------------------------+-----------------------+
| Variable_name            | Value                 |
+--------------------------+-----------------------+
| require_secure_transport | OFF                   |
| secure_file_priv         | /var/lib/mysql-files/ |
+--------------------------+-----------------------+

SELECT * FROM account INTO OUTFILE "/var/lib/mysql-files/account.txt";
```

```sql
mysqldump -uroot -p -T "/var/lib/mysql-files/" testdb account

mysqldump -uroot -p -T "/var/lib/mysql-files/" testdb account --fields-terminated-by=',' --fields-optionally-enclosed-by='\"'

mysql -uroot -p --execute="SELECT * FROM account;" testdb> "/var/lib/mysql-files/account.txt"

#/G的格式
mysql -uroot -p --vertical --execute="SELECT * FROM account;" testdb > "/var/lib/mysql-files/account_1.txt"

#xml格式
mysql -uroot -p --xml --execute="SELECT * FROM account;" testdb>"/var/lib/mysql-files/account_3.xml"
```

#### 4.2 导入文件

```sql
SELECT * FROM testdb.account INTO OUTFILE '/var/lib/mysql-files/account_0.txt';

#删除数据
DELETE FROM testdb.account;
#回复数据
LOAD DATA INFILE '/var/lib/mysql-files/account_0.txt' INTO TABLE testdb.account;
```

```sql
SELECT * FROM testdb.account INTO OUTFILE '/var/lib/mysql-files/account_1.txt' FIELDS TERMINATED BY ',' ENCLOSED BY '\"';

#删除数据
DELETE FROM testdb.account;
#回复数据
LOAD DATA INFILE '/var/lib/mysql-files/account_1.txt' INTO TABLE testdb.account FIELDS TERMINATED BY ',' ENCLOSED BY '\"';
```

```sql
SELECT * FROM testdb.account INTO OUTFILE '/var/lib/mysql-files/account.txt' FIELDS TERMINATED BY ',' ENCLOSED BY '\"';

#删除数据
DELETE FROM testdb.account;
#回复数据
 mysqlimport -uroot -p testdb '/var/lib/mysql-files/account.txt' --fields-terminated-by=',' --fields-optionally-enclosed-by='\"'
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
