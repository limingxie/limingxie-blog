---
author: "li_mingxie"
title: "【ES笔记】Elasticsearch_容器服务(1)"
date: 2022-08-08T23:28:49+08:00
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

这一篇整理了如何启动Elasticsearch容器。  <!--more-->

## 1.拉取,执行

```
#pull image
docker pull elasticsearch:8.3.3

#run container 
docker run -d --name es -v /Users/limingxie/volumes/elasticsearch/00/config:/usr/share/elasticsearch/config/:rw --net bridge -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:8.3.3
```

如果没有

## 2.去掉验证

之后你会发现连不上...^^;;
是因为ssl认证和账号密码验证所致。  

进入到以下路径  
/config/elasticsearch.yml  
xpack.security.http.ssl:enabled 设置成 false  
xpack.security.enabled 属性设置为 false  

```bash
# Enable security features
xpack.security.enabled: false

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false
  keystore.path: certs/http.p12
```

## 3.验证是否正常启动

get请求 <http://localhost:9200>  
如有以下返回值，es就正常起来了。  
可以随便做测试了，搞砸了删容器从新跑一个...^^  

```bash
$ curl 'http://localhost:9200'
{
  "name" : "6c084a345c31",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "D7abMrBRQ2qMMGPeCzKT3A",
  "version" : {
    "number" : "8.3.3",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "801fed82df74dbe537f89b71b098ccaff88d2c56",
    "build_date" : "2022-07-23T19:30:09.227964828Z",
    "build_snapshot" : false,
    "lucene_version" : "9.2.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

## 5.集群模式

#### 5.1 docker pull

```bash
#docker pull image
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.5.2
```

#### 5.2 docker compose

创建 docker-compose.yml 文件

```
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.2
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /Users/limingxie/volumes/elasticsearch/00/data01:/usr/share/elasticsearch/data
    ports:
      - 9201:9200
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.2
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /Users/limingxie/volumes/elasticsearch/00/data02:/usr/share/elasticsearch/data
    ports:
      - 9202:9200
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.2
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - /Users/limingxie/volumes/elasticsearch/00/data03:/usr/share/elasticsearch/data
    ports:
      - 9203:9200
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

```bash
#docker run
docker-compose up
```

#### 5.3 验证服务

```bash
$ curl 'http://localhost:9201'

{
  "name" : "es01",
  "cluster_name" : "es-docker-cluster",
  "cluster_uuid" : "OhJKn-jQTnucdPqQh0715g",
  "version" : {
    "number" : "7.5.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "8bec50e1e0ad29dad5653712cf3bb580cd1afcdf",
    "build_date" : "2020-01-15T12:11:52.313576Z",
    "build_snapshot" : false,
    "lucene_version" : "8.3.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
