---
author: "li_mingxie"
title: "【ES笔记】Elasticsearch_结构和常用API简介(2)"
date: 2022-08-09T23:28:49+08:00
tags: [
    "elasticsearch",
    "es",
    "database",
    "index",
]
categories: [
    "database",
    "elasticsearch",
]
---

简单的做一下Elasticsearch的结构和常用语法的记录。  <!--more-->

## 1.Elasticsearch基本概念

#### Cluster 集群

群集是一个或多个节点（服务器）的集合， 这些节点共同保存整个数据，并在所有节点上提供联合索引和搜索功能。  
一个集群由一个唯一集群ID确定，并指定一个集群名（默认为“elasticsearch”）。

#### Node节点

节点是单个服务器实例，它是群集的一部分，可以存储数据，并参与群集的索引和搜索功能。  
节点的名称默认为一个随机的通用唯一标识符（UUID），确定在启动时分配给该节点。  
我们可以通过群集名配置节点以连接特定的群集。默认情况下，每个节点设置加入名为“elasticSearch”的集群。

#### Index索引

索引是具有相似特性的文档集合。  
类似于RDS的数据库(database)。  

#### Type类型

在索引中，可以定义一个或多个类型。类型是索引的逻辑类别/分区。  
类似于RDS的表(Table)。  ==> 后续版不用这个类型了。

#### Document文档

文档是可以被索引的信息的基本单位。本文件的表示形式为JSON（JavaScript Object Notation）格式。  
类似于RDS的行(Row)。  

#### Field(字段)

类似于RDS的列(Column)。  

#### 映射(Mapping)

类似于RDS的Schema。  

#### 分片(Shards) 和 副本(Replicas)

当你创建一个索引，你可以简单地定义你想要的分片数量。  
每个分片本身是一个全功能的、独立的“指数”，可以托管在集群中的任何节点。

Shards分片的重要性主要体现在以下两个特征：

1. 副本为分片或节点失败提供了高可用性。但是一个副本的分片不会分配在同一个节点作为原始的或主分片，副本是从主分片那里复制过来的。
2. 副本允许用户扩展你的搜索量或吞吐量，因为搜索可以在所有副本上并行执行。

## 2.Elasticsearch常用API

#### 2.1 查看健康状态

```
    curl -X GET 127.0.0.1:9200/_cat/health?v
```

结果：

```
    epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
    1564726309 06:11:49  elasticsearch yellow          1         1      3   3    0    0        1             0                  -                 75.0%
```

#### 2.2 查询当前es集群中所有的indices

```
    curl -X GET 127.0.0.1:9200/_cat/indices?v
```

结果：

```
    health status index                uuid                   pri rep docs.count docs.deleted store.size pri.store.size
    green  open   .kibana_task_manager LUo-IxjDQdWeAbR-SYuYvQ   1   0          2            0     45.5kb         45.5kb
    green  open   .kibana_1            PLvyZV1bRDWex05xkOrNNg   1   0          4            1     23.9kb         23.9kb
    yellow open   user                 o42mIpDeSgSWZ6eARWUfKw   1   1          0            0       283b           283b
```

#### 2.3 创建索引

```
    curl -X PUT 127.0.0.1:9200/www
```

结果：

```
    {"acknowledged":true,"shards_acknowledged":true,"index":"www"}
```

#### 2.4 删除索引

```
    curl -X DELETE 127.0.0.1:9200/www
```

结果：

```
    {"acknowledged":true}
```

#### 2.5 插入记录

```
    curl -H "ContentType:application/json" -X POST 127.0.0.1:9200/user/person -d '
    {
        "name": "LMH",
        "age": 18,
        "married": true
    }'
```

结果：

```
{
    "_index": "user",
    "_type": "person",
    "_id": "MLcwUWwBvEa8j5UrLZj4",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 3,
    "_primary_term": 1
}
```

#### 2.6 PUT方法，需要传入id

```
    curl -H "ContentType:application/json" -X PUT 127.0.0.1:9200/user/person/4 -d '
    {
        "name": "LMH",
        "age": 18,
        "married": false
    }'
```

#### 2.7 全检索

```
    curl -X GET 127.0.0.1:9200/user/person/_search
```

#### 2.8 按条件检索

```
    curl -H "ContentType:application/json" -X PUT 127.0.0.1:9200/user/person/4 -d '
    {
        "query":{
            "match": {"name": "LMH"}
        }    
    }'
```

#### 2.9 条件检索size字段

```
    curl -H "ContentType:application/json" -X PUT 127.0.0.1:9200/user/person/4 -d '
    {
        "query":{
            "match": {"name": "LMH"},
            "size": 2
        }    
    }'
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
