---
author: "li_mingxie"
title: "【mysql笔记】EXPLAIN"
date: 2022-06-18T12:28:49+08:00
tags: [
    "mysql",
    "explain",
]
categories: [
    "docker"
]
---

我们时常遇到需要mysql数据反应缓慢，需优化的时候。  
如果对explain有所了解，你可以现查现优化。  

## 1.慢查询

谈explain优化之前先看看如何查找慢查询。  

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

## 2.具体查看慢查询分析工具和命令

可以使用muysqldumpslow工具查看，也可以查看binlog日志。

```sql
#查看 SQL 执行成本  
show variables like 'profiling'  

sql_mode  
```

**一些常用的性能参数如下:**  

* Connections:连接MySQL服务器的次数。
* Uptime:MySQL服务器的上 线时间。
* Slow_queries:慢查询的次数。
* Innodb_rows_read:Select查询返回的行数
* Innodb_rows_inserted:执行INSERT操作插入的行数
* Innodb_rows_updated:执行UPDATE操作更新的 行数
* Innodb_rows_deleted:执行DELETE操作删除的行数
* Com_select:查询操作的次数。
* Com_insert:插入操作的次数。对于批量插入的 INSERT 操作，只累加一次。
* Com_update:更新操作 的次数。
* Com_delete:删除操作的次数

## 3.EXPLAIN

```sql
mysql> explain select * from app where id > 1;
+----+-------------+-------------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table       | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | application | NULL       | ALL  | PRIMARY       | NULL | NULL    | NULL |  172 |    33.33 | Using where |
+----+-------------+-------------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

看看每个列的说明

|列名|说明|
|---|---|
|id| 在一个大的查询语句中每个SELECT关键字都对应一个 唯一的id |
|select_type| select关键字对应的那个查询的类型 |
|table| 表明 |
|partitions|  匹配的分区信息 |
|**type**| 针对单表的访问方法 |
|possible_keys| 可能用到的索引 |
|key| 实际上使用的索引 |
|**key_len**| 实际上使用到的索引长度 |
|ref| 当使用索引列等值查询时，与索引列进行等值匹配的对象信息 |
|**rows**| 预估的需要读取的记录条数 |
|filtered| 某个表经过搜索条件过滤后剩余记录条数的百分比 |
|**Extra**| 一些额外的信息 |

其中 type，key_len，rows，Extra 的结果比较重要。

```
type: 结果值从最好到最坏依次是:
**system** > **const** > **eq_ref** > **ref** >  
fulltext > ref_or_null > index_merge > unique_subquery > index_subquery >  
**range** > **index** > **ALL**
SQL 性能优化的目标:至少要达到 range 级别，要求是 ref 级别，最好是 consts级别。
```

>key_len 长度计算  
>varchar(10)变长字段且允许NULL = 10 * ( character set: utf8=3,gbk=2,latin1=1)+1(NULL)+2(变长字段) => 33  

#### explain 没考虑到的地方

EXPLAIN不考虑各种Cache
EXPLAIN不能显示MySQL在执行查询时所作的优化工作  
EXPLAIN不会告诉你关于触发器、存储过程的信息或用户自定义函数对查询的影响情况  
部分统计信息是估算的，并非精确值

## 4.explain 4种输出格式

#### 4.1 传统格式  

```sql
EXPLAIN SELECT ....
```

#### 4.2 JSON格式  

```sql
EXPLAIN FORMAT=JSON SELECT ....
```

JSON格式多出了cost的内容。

```json
"cost_info": {
    "read_cost": "1840.84", //IO 成本, 检测 rows × (1 - filter) 条记录的 CPU 成本
    "eval_cost": "193.76", //检测 rows × filter 条记录的成本。
    "prefix_cost": "2034.60", //read_cost + eval_cost
    "data_read_per_join": "1M" //表示在此次查询中需要读取的数据量
}
```

#### 4.3 TREE格式  

```sql
EXPLAIN FORMAT=tree SELECT ....
```

#### 4.4 视化输出  

可视化输出，可以通过MySQL Workbench可视化查看MySQL的执行计划。  
通过点击Workbench的放大镜图标，即可生成可视化的查询计划。

## 5.Sys schema视图摘要

1. 主机相关:以host_summary开头，主要汇总了IO延迟的信息。
2. Innodb相关:以innodb开头，汇总了innodb buffer信息和事务等待innodb锁的信息。
3. I/o相关:以io开头，汇总了等待I/O、I/O使用量情况。
4. 内存使用情况:以memory开头，从主机、线程、事件等角度展示内存的使用情况
5. 连接与会话信息:processlist和session相关视图，总结了会话相关信息。
6. 表相关:以schema_table开头的视图，展示了表的统计信息。
7. 索引信息:统计了索引的使用情况，包含冗余索引和未使用的索引情况。
8. 语句相关:以statement开头，包含执行全表扫描、使用临时表、排序等的语句信息。
9. 用户相关:以user开头的视图，统计了用户使用的文件I/O、执行语句统计信息。
10. 等待事件相关信息:以wait开头，展示等待事件的延迟情况。

#### 索引情况

```sql
#1. 查询冗余索引
select * from sys.schema_redundant_indexes;
#2. 查询未使用过的索引
select * from sys.schema_unused_indexes;
#3. 查询索引的使用情况
select index_name,rows_selected,rows_inserted,rows_updated,rows_deleted 
from sys.schema_index_statistics where table_schema='dbname' ;
```

#### 表相关

```sql
# 1. 查询表的访问量
select table_schema,table_name,sum(io_read_requests+io_write_requests) as io 
from sys.schema_table_statistics group by table_schema,table_name order by io desc;
# 2. 查询占用bufferpool较多的表
select object_schema,object_name,allocated,data
from sys.innodb_buffer_stats_by_table order by allocated limit 10;
# 3. 查看表的全表扫描情况
select * from sys.statements_with_full_table_scans where db='dbname';
```

#### 语句相关

```sql
#1. 监控SQL执行的频率
select db,exec_count,query from sys.statement_analysis order by exec_count desc;
#2. 监控使用了排序的SQL
select db,exec_count,first_seen,last_seen,query from sys.statements_with_sorting limit 1;
#3. 监控使用了临时表或者磁盘临时表的SQL
select db,exec_count,tmp_tables,tmp_disk_tables,query
from sys.statement_analysis where tmp_tables>0 or tmp_disk_tables >0 
order by (tmp_tables+tmp_disk_tables) desc;
```

#### IO相关

```sql
#1. 查看消耗磁盘IO的文件
select file,avg_read,avg_write,avg_read+avg_write as avg_io
from sys.io_global_by_file_by_bytes order by avg_read limit 10;
```

Innodb 相关

```sql
 #1. 行锁阻塞情况
select * from sys.innodb_lock_waits;
```

## 6.其他

```sql
#可以查看mysql执行前优化后的内容
SHOW WARNINGS  

#trace
SET optimizer_trace="enabled=on",end_markers_in_json=on;
set optimizer_trace_max_mem_size=1000000;

执行语句后查看
select * from information_schema.optimizer_trace\G
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
