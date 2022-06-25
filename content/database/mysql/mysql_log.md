---
author: "li_mingxie"
title: "【mysql笔记】redo和undo日志"
date: 2922-07-08T23:28:49+08:00
tags: [
    "mysql",
    "explain",
]
categories: [
    "mysql",
    "database",
]
---

## 1.基本概念

事务: 一组逻辑操作单元，使数据从一种状态变换到另一种状态。  
事务处理的原则:保证所有事务都作为 一个工作单元 来执行，即使出现了故障，都不能改变这种执行方式。  
当在一个事务中执行多个操作时，要么所有的事务都被提交(commit)，那么这些修改就永久地保存下来;  
要么数据库管理系统将放弃所作的所有修改 ，整个事务回滚(rollback)到最初状态。  

## 2.事务的ACID特性

#### 原子性(atomicity)

原子性是指事务是一个不可分割的工作单位，要么全部提交，要么全部失败回滚。  

#### 一致性(consistency)

(国内很多网站上对一致性的阐述有误，具体你可以参考 Wikipedia 对 Consistency 的阐述)  
根据定义，一致性是指事务执行前后，数据从一个 合法性状态 变换到另外一个 合法性状态 。这种状态 是 语义上 的而不是语法上的，跟具体的业务有关。  

#### 隔离型(isolation)

事务的隔离性是指一个事务的执行，即一个事务内部的操作及使用的数据对 并发 的 其他事务是隔离的，并发执行的各个事务之间不能互相干扰。  

#### 持久性(durability)

持久性是指一个事务一旦被提交，它对数据库中数据的改变就是 永久性的 ，接下来的其他操作和数据库故障不应该对其有任何影响。  

## 3.事务的状态

#### 活动的(active)

事务对应的数据库操作正在执行过程中时，我们就说该事务处在 活动的 状态。  

#### 部分提交的(partially committed)

当事务中的最后一个操作执行完成，但由于操作都在内存中执行，所造成的影响并 没有刷新到磁盘 时，我们就说该事务处在 部分提交的 状态。  

#### 失败的(failed)

当事务处在 活动的 或者 部分提交的 状态时，可能遇到了某些错误(数据库自身的错误、操作系统 错误或者直接断电等)而无法继续执行，  
或者人为的停止当前事务的执行，我们就说该事务处在 失败的状态。  
  
#### 中止的(aborted)

如果事务执行了一部分而变为 失败的 状态，那么就需要把已经修改的事务中的操作还原到事务执 行前的状态。  

#### 提交的(committed)

当一个处在 部分提交的 状态的事务将修改过的数据都 同步到磁盘 上之后，我们就可以说该事务处在了提交状态  

## 3.显式事务

步骤1: START TRANSACTION 或者 BEGIN ，作用是显式开启一个事务。

```sql
mysql> BEGIN;
#或者
mysql> START TRANSACTION;
```

1 **READ ONLY** :标识当前事务是一个只读事务，也就是属于该事务的数据库操作只能读取数据，而不 能修改数据。  
2 **READ WRITE** :标识当前事务是一个 读写事务 ，也就是属于该事务的数据库操作既可以读取数据， 也可以修改数据。  
3 **WITH CONSISTENT** SNAPSHOT :启动一致性读。  

```sql
# 提交事务。当提交事务后，对数据库的修改是永久性的
mysql> COMMIT

# 回滚事务。即撤销正在进行的所有没有提交的修改
mysql> ROLLBACK

# 将事务回滚到某个保存点
mysql> ROLLBACK TO [SAVEPOINT]
```

## 4.隐式事务

MySQL中有一个系统变量 autocommit :

```sql
mysql> SHOW VARIABLES LIKE 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+
1 row in set (0.01 sec)

SET autocommit = OFF; 
#或
SET autocommit = 0;
```

## 5.事务隔离级别

脏写Dirty Write > 脏读Dirty Read > 不可重复读Non-Repeatable Read > 幻读Phantom

#### READ UNCOMMITTED

读未提交，在该隔离级别，所有事务都可以看到其他未提交事务的执行结 果。不能避免脏读、不可重复读、幻读。

#### READ COMMITTED

读已提交，它满足了隔离的简单定义:一个事务只能看见已经提交事务所做 的改变。这是大多数数据库系统的默认隔离级别(但不是MySQL默认的)。  
可以避免脏读，但不可 重复读、幻读问题仍然存在。  

#### REPEATABLE READ

可重复读，事务A在读到一条数据之后，此时事务B对该数据进行了修改并提 交，那么事务A再读该数据，读到的还是原来的内容。  
可以避免脏读、不可重复读，但幻读问题仍 然存在。这是MySQL的默认隔离级别。

#### SERIALIZABLE

可串行化，确保事务可以从一个表中读取相同的行。在这个事务持续期间，禁止 其他事务对该表执行插入、更新和删除操作。  
所有的并发问题都可以避免，但性能十分低下。能避 免脏读、不可重复读和幻读

|隔离级别 | 脏读可能性 | 不可重复读可能性 | 幻读可能性 |加锁读
|--|--|--|--|--|
|READ UNCOMMITTED | Yes | Yes | Yes | No |
|READ COMMITTED | No | Yes | Yes | No |
|REPEATABLE READ | No | No | Yes | No |
|SERIALIZABLE | No | No | No | Yes |

```sql
# 查看隔离级别，MySQL 5.7.20的版本之前: mysql> SHOW VARIABLES LIKE 'tx_isolation'; 
+---------------+-----------------+
| Variable_name | Value           | 
+---------------+-----------------+
| tx_isolation  | REPEATABLE-READ | 
+---------------+-----------------+

# MySQL 5.7.20版本之后，引入transaction_isolation来替换tx_isolation
# 查看隔离级别，MySQL 5.7.20的版本及之后:
mysql> SHOW VARIABLES LIKE 'transaction_isolation'; 
+-----------------------+-----------------+
| Variable_name         | Value           | 
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.02 sec)
#或者不同MySQL版本中都可以使用的: 
SELECT @@transaction_isolation;
```

#### 如何设置事务的隔离级别

```sql
SET [GLOBAL|SESSION] TRANSACTION ISOLATION LEVEL 隔离级别;
#其中，隔离级别格式:
> READ UNCOMMITTED
> READ COMMITTED
> REPEATABLE READ
> SERIALIZABLE
```

## 6.事务的常见分类

从事务理论的角度来看，可以把事务分为以下几种类型:

扁平事务(Flat Transactions)
带有保存点的扁平事务(Flat Transactions with Savepoints) 链事务(Chained Transactions)
嵌套事务(Nested Transactions)
分布式事务(Distributed Transactions)

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
