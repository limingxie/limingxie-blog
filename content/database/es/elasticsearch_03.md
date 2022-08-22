---
author: "li_mingxie"
title: "【ES笔记】Elasticsearch_常用API(3)"
date: 2022-08-10T23:28:49+08:00
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

整理了Elasticsearch常用的API方法。  <!--more-->

## 1.索引操作

#### 1.1 创建索引

```
PUT请求 ：http://127.0.0.1:9200/shopping

{
    "acknowledged"【响应结果】: true, 
    "shards_acknowledged"【分片结果】: true, 
    "index"【索引名称】: "shopping"
}
```

#### 1.2 查看所有索引

```
GET请求 ：http://127.0.0.1:9200/_cat/indices?v
```

#### 1.3 查看单个索引

```
GET请求 ：http://127.0.0.1:9200/shopping

{
    "shopping"【索引名】: {        
        "aliases"【别名】: {},
        "mappings"【映射】: {},
        "settings"【设置】: {
            "index"【设置 - 索引】: {
                "creation_date"【设置 - 索引 - 创建时间】: "1614265373911",
                "number_of_shards"【设置 - 索引 - 主分片数量】: "1",
                "number_of_replicas"【设置 - 索引 - 副分片数量】: "1",
                "uuid"【设置 - 索引 - 唯一标识】: "eI5wemRERTumxGCc1bAk2A",
                "version"【设置 - 索引 - 版本】: {
                    "created": "7080099"
                },
                "provided_name"【设置 - 索引 - 名称】: "shopping"
            }
        }
    }
}
```

#### 1.4 删除索引

```
DELETE请求 ：http://127.0.0.1:9200/shopping
```

## 2.文档操作

#### 2.1 创建文档

```
POST请求 ：http://127.0.0.1:9200/shopping/_doc
{
    "title":"小米手机",
    "category":"小米",
    "images":"http://www.gulixueyuan.com/xm.jpg",
    "price":3999.00
}

{
    "_index"【索引】: "shopping",
    "_type"【类型-文档】: "_doc",
    "_id"【唯一标识】: "Xhsa2ncBlvF_7lxyCE9G", #可以类比为MySQL中的主键，随机生成
    "_version"【版本】: 1,
    "result"【结果】: "created", #这里的create表示创建成功
    "_shards"【分片】: {
        "total"【分片 - 总数】: 2,
        "successful"【分片 - 成功】: 1,
        "failed"【分片 - 失败】: 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}
```

#### 2.2 查看文档

```
GET请求 ：http://127.0.0.1:9200/shopping/_doc/1

{
    "_index"【索引】: "shopping",
    "_type"【文档类型】: "_doc",
    "_id": "1",
    "_version": 2,
    "_seq_no": 2,
    "_primary_term": 2,
    "found"【查询结果】: true, # true表示查找到，false表示未查找到
    "_source"【文档源信息】: {
        "title": "华为手机",
        "category": "华为",
        "images": "http://www.gulixueyuan.com/hw.jpg",
        "price": 4999.00
    }
}
```

#### 2.3 修改文档

```
POST请求 ：http://127.0.0.1:9200/shopping/_doc/1
{
    "title":"华为手机",
    "category":"华为",
    "images":"http://www.gulixueyuan.com/hw.jpg",
    "price":4999.00
}

{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version"【版本】: 2,
    "result"【结果】: "updated", # updated表示数据被更新
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 2,
    "_primary_term": 2
}

```

#### 2.4 修改字段

```
POST请求 ：http://127.0.0.1:9200/shopping/_update/1
{ 
  "doc": {
    "price":3000.00
  } 
}
```

#### 2.5 删除文档

```
DELETE请求 ：http://127.0.0.1:9200/shopping/_doc/1

{
    "_index": "shopping",
    "_type": "_doc",
    "_id": "1",
    "_version"【版本】: 4, #对数据的操作，都会更新版本
    "result"【结果】: "deleted", # deleted表示数据被标记为删除
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 4,
    "_primary_term": 2
}
```

## 3.映射操作

#### 3.1 创建映射

```
PUT请求 ：http://127.0.0.1:9200/student/_mapping
{
  "properties": {
    "name":{
      "type": "text",
      "index": true
    },
    "sex":{
      "type": "text",
      "index": false
    },
    "age":{
      "type": "long",
      "index": false
    }
  }
}
```

* 字段名：任意填写，下面指定许多属性，例如：title、subtitle、images、price
* type：类型，Elasticsearch中支持的数据类型非常丰富，说几个关键的：
  * String类型，又分两种：
        text：可分词
        keyword：不可分词，数据会作为完整字段进行匹配
  * Numerical：数值类型，分两类
        基本数据类型：long、integer、short、byte、double、float、half_float
        浮点数的高精度类型：scaled_float
  * Date：日期类型
  * Array：数组类型
  * Object：对象
* index：是否索引，默认为true，也就是说你不进行任何配置，所有字段都会被索引。
    true：字段会被索引，则可以用来进行搜索
    false：字段不会被索引，不能用来搜索
* store：是否将数据进行独立存储，默认为false
    原始的文本会存储在_source里面，默认情况下其他提取出来的字段都不是独立存储的，是从_source里面提取出来的。  
    当然你也可以独立的存储某个字段，只要设置"store": true即可，获取独立存储的字段要比从_source中解析快得多，  
    但是也会占用更多的空间，所以要根据实际业务需求来设置。
* analyzer：分词器，这里的ik_max_word即使用ik分词器

#### 3.2 查看映射

```
GET请求 ：http://127.0.0.1:9200/student/_mapping
```

#### 3.3 索引映射关联

```
PUT请求 ：http://127.0.0.1:9200/student1
{
  "settings": {},
  "mappings": {
   "properties": {
  "name":{
    "type": "text",
    "index": true
    
  },
  "sex":{
    "type": "text",
    "index": false
  },
  "age":{
    "type": "long",
    "index": false
  }
   }
  }
}
```

## 4.高级查询

## 4.1 查询所有文档

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "match_all": {}
  }
}
# "query"：这里的query代表一个查询对象，里面可以有不同的查询属性
# "match_all"：查询类型，例如：match_all(代表查询所有)， match，term ， range 等等
# {查询条件}：查询条件会根据类型的不同，写法也有差异

{
  "took【查询花费时间，单位毫秒】" : 1116,
  "timed_out【是否超时】" : false,
  "_shards【分片信息】" : {
    "total【总数】" : 1,
    "successful【成功】" : 1,
    "skipped【忽略】" : 0,
    "failed【失败】" : 0
  },
  "hits【搜索命中结果】" : {
     "total"【搜索条件匹配的文档总数】: {
         "value"【总命中计数的值】: 3,
         "relation"【计数规则】: "eq" # eq 表示计数准确， gte表示计数不准确
     },
    "max_score【匹配度分值】" : 1.0,
    "hits【命中结果集合】" : [
       。。。
      }
    ]
  }
}
```

#### 4.2 匹配查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "match": {
        "name":"zhangsan"
    }
  }
}
```

#### 4.3 字段匹配查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "multi_match": {
        "query": "zhangsan",
        "fields": ["name","nickname"]
    }
  }
}
```

#### 4.4 关键字精确查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "term": {
      "name": {
        "value": "zhangsan"
      }
    }
  }
}
```

#### 4.5 多关键字精确查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "terms": {
      "name": ["zhangsan","lisi"]
    }
  }
}
```

#### 4.6 指定查询字段

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "_source": ["name","nickname"],  
  "query": {
    "terms": {
      "nickname": ["zhangsan"]
    }
  }
}
```

#### 4.7 过滤字段

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "_source": {
    "includes": ["name","nickname"]
  },  
  "query": {
    "terms": {
      "nickname": ["zhangsan"]
    }
  }
}

GET请求 ：http://127.0.0.1:9200/student/_search
{
  "_source": {
    "excludes": ["name","nickname"]
  },  
  "query": {
    "terms": {
      "nickname": ["zhangsan"]
    }
  }
}
```

#### 4.8 组合查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "zhangsan"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "age": "40"
          }
        }
      ],
      "should": [
        {
          "match": {
            "sex": "男"
          }
        }
      ]
    }
  }
}
```

#### 4.9 范围查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "range": {
      "age": {
        "gte": 30,
        "lte": 35
      }
    }
  }
}
```

* gt 大于>
* gte 大于等于>=
* lt 小于<
* lte 小于等于<=

#### 4.10 模糊查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "fuzzy": {
      "title": {
        "value": "zhangsan"
      }
    }
  }
}

GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "fuzzy": {
      "title": {
        "value": "zhangsan",
  "fuzziness": 2
      }
    }
  }
}
```

#### 4.11 单字段排序

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "match": {
        "name":"zhangsan"
    }
  },
  "sort": [{
    "age": {
        "order":"desc"
    }
  }]
}
```

#### 4.12 多字段排序

```
GET请求 ：http://127.0.0.1:9200/student/_search
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    },
    {
      "_score":{
        "order": "desc"
      }
    }
  ]
}
```

#### 4.13 高亮查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "match": {
      "name": "zhangsan"
    }
  },
  "highlight": {
    "pre_tags": "<font color='red'>",
    "post_tags": "</font>",
    "fields": {
      "name": {}
    }
  }
}
```

* pre_tags：前置标签
* post_tags：后置标签
* fields：需要高亮的字段
* title：这里声明title字段需要高亮，后面可以为这个字段设置特有配置，也可以空

#### 4.14 分页查询

```
GET请求 ：http://127.0.0.1:9200/student/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ],
  "from": 0,
  "size": 2
}
```

#### 4.15 聚合查询

```
#对某个字段取最大值max
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "max_age":{
        "max":{"field":"age"}
      }
    },
    "size":0
}

#对某个字段取最小值min
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "min_age":{
        "min":{"field":"age"}
      }
    },
    "size":0
}

#对某个字段求和sum
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "sum_age":{
        "sum":{"field":"age"}
      }
    },
    "size":0
}

#对某个字段取平均值avg
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "avg_age":{
        "avg":{"field":"age"}
      }
    },
    "size":0
}

#对某个字段的值进行去重之后再取总数
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "distinct_age":{
        "cardinality":{"field":"age"}
      }
    },
    "size":0
}

#State聚合
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "stats_age":{
        "stats":{"field":"age"}
      }
    },
    "size":0
}
```

#### 4.16 桶聚合查询

```
#terms聚合，分组统计
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "age_groupby":{
        "terms":{"field":"age"}
      }
    },
    "size":0
} 

#在terms分组下再进行聚合
GET请求 ：http://127.0.0.1:9200/student/_search
{
    "aggs":{
      "age_groupby":{
        "terms":{"field":"age"}
      }
    },
    "size":0
} 
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
