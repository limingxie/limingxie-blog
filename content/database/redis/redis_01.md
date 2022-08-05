---
author: "li_mingxie"
title: "【redis笔记】redis_常用命令(1)"
date: 2022-08-02T23:28:49+08:00
tags: [
    "redis",
]
categories: [
    "redis"
]
---

这一篇整理了redis的常用命令。<!--more-->  

## 1.安装目录以及启动

一般默认的安装路径是: `/usr/local/bin`

```bash
$ ls /usr/local/bin
redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server
```

* redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何(服务启动起来后执行)
* redis-check-aof：修复有问题的AOF文件，rdb和aof后面讲
* redis-check-dump：修复有问题的dump.rdb文件
* redis-sentinel：Redis集群使用
* **`redis-server`**：Redis服务器启动命令
* **`redis-cli`**：客户端，操作入口

## 2.数据类型

```
# 查询当前库的所有键
keys *

# 判断某个键是否存在
exists <key>  

# 查看键的类型
type <key>

# 删除某个键
del <key>

# 为键值设置过期时间，单位秒。
expire <key> <seconds>  

# 查看还有多少秒过期，-1表示永不过期，-2表示已过期
ttl <key>

# 查看当前数据库的key的数量
dbsize

# 清空当前库
Flushdb

# 通杀全部库
Flushall
```

### 2.1 string

|命令|说明|
|----|----|
|**`get <key>`**|查询对应键值|
|**`set <key> <value>`**|添加键值对|
|**`append <key> <value>`**|将给定的value追加到原值的末尾|
|**`strlen <key>`**|获得值的长度|
|**`setnx <key> <value>`**|只有在 key 不存在时设置 key 的值|
|**`incr <key>`**|将 key 中储存的数字值增1|
|**`decr <key>`**|将 key 中储存的数字值减1|
|**`incrby / decrby <key> <步长>`**|将 key 中储存的数字值增减。自定义步长。|
|**`mset <key1>  <value1>  <key2>  <value2>`** .....|同时设置一个或多个 key-value对|
|**`mget  <key1> <key2> <key3> .....`**|同时获取一个或多个 value|
|**`msetnx <key1> <value1> <key2> <value2>  .....`**|同时设置一个或多个 key-value 对，当且仅当所有给定key都不存在|
|**`getrange <key> <起始位置> <结束位置>`**|获得值的范围，类似java中的substring|
|**`setrange <key> <起始位置> <value>`**|用value覆写key所储存的字符串值，从<起始位置>开始|
|**`setex <key> <过期时间>  <value>`**|设置键值的同时，设置过期时间，单位秒|
|**`getset <key> <value>`**|以新换旧，设置了新值同时获得旧值|

### 2.2 set

|命令|说明|
|----|----|
|**`sadd <key> <value1> <value2>.....`**|将一个或多个member元素加入到集合key当中，已经存在于集合的member元素将被忽略。|
|**`smembers <key>`**| 取出该集合的所有值。|
|**`sismember <key> <value>`**|判断集合key是否为含有该value值，有返回1，没有返回0。|
|**`scard <key>`**|返回该集合的元素个数。|
|**`srem <key> <value1> <value2> ....`**|删除集合中的某个元素。|
|**`spop <key>`**|随机从该集合中吐出一个值。|
|**`srandmember <key> <n>`**|随机从该集合中取出n个值。不会从集合中删除|
|**`sinter <key1> <key2>`**|返回两个集合的交集元素。|
|**`sunion <key1> <key2>`**|返回两个集合的并集元素。|
|**`sdiff <key1> <key2>`**|返回两个集合的差集元素。|

### 2.3 list

|命令|说明|
|----|----|
|**`lpush/rpush <key> <value1> <value2> <value3>....`**|从左边/右边插入一个或多个值。|
|**`lpop/rpop <key>`**|从左边/右边吐出一个值。|
|**`rpoplpush <key1> <key2>`**|从key1列表右边吐出一个值，插到key2列表左边。|
|**`lrange <key> <start> <stop>`**|按照索引下标获得元素(从左到右)|
|**`lindex <key> <index>`**|按照索引下标获得元素(从左到右)|
|**`llen <key>`**|获得列表长度|
|**`linsert <key> before <value> <newvalue>`**|在value的后面插入newvalue插入值|
|**`lrem <key> <n> <value>`**|取值为正数，从左边删除n个value(从左到右),负数右左，0全部|

### 2.4 hash

|命令|说明|
|----|----|
|**`hset <key> <field> <value>`**|给key集合中的field键赋值value|
|**`hget <key1> <field>`**|从key1集合field取出 value|
|**`hmset <key1> <field1> <value1> <field2> <value2>...`**|批量设置hash的值|
|**`hexists key <field>`**|查看哈希表key中，给定域field是否存在。|
|**`hkeys <key>`**|列出该hash集合的所有field|
|**`hvals <key>`**| 列出该hash集合的所有value|
|**`hincrby <key> <field> <increment>`**|为哈希表key中的域field的值加上增量increment|
|**`hsetnx <key> <field> <value>`**|将哈希表key中的域field的值设置为value，当且仅当域field不存在。|

### 2.5 zset

```
#将一个或多个member元素及其score值加入到有序集key当中。
zadd <key> <score1> <value1> <score2> <value2>...

#返回有序集key中，下标在start，stop之间的元素。  
#带WITHSCORES，可以让分数一起和值返回到结果集。
zrange <key> <start> <stop> [WITHSCORES]`**

#返回有序集key中，所有score值介于min和max之间(包括等于min或max)的成员。  
#有序集成员按 score 值递增(从小到大)次序排列。 
zrangebyscore key min max [withscores] [limit offset count]

#同上，改为从大到小排列。 
zrevrangebyscore key max min [withscores] [limit offset count]

#为元素的score加上增量
zincrby <key> <increment> <value>

#删除该集合下，指定值的元素 
zrem <key> <value>  

#统计该集合，分数区间内的元素个数 
zcount <key> <min> <max> 

#返回该值在集合中的排名，从0开始。
zrank <key> <value> 
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
