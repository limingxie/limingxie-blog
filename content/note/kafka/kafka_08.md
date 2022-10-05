---
author: "li_mingxie"
title: "【kafka笔记】kafka_offset(8)"
date: 2022-10-12T23:28:49+08:00
tags: [
    "kafka",
]
categories: [
    "kafka"
]
---

这一篇整理了kafka的 **`offset`** 相关的内容。<!--more-->  

## 1.offset 位移

__consumer_offsets 主题里面采用key和value的方式存储数据。  
key是group.id+topic+分区号，value 就是当前 offset 的值。  
每隔一段时间，kafka内部会对这个topic进行compact，也就是每个group.id+topic+分区号就保留最新数据。

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_24.png)  
![kafka_24](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_24.png)

在配置文件 config/consumer.properties中添加配置exclude.internal.topics=false，  
默认是true，表示不能消费系统主题。为了查看该系统主题数据，所以该参数修改为false。

## 2.自动提交 offset  

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_25.png)  
![kafka_25](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_25.png)

自动提交offset的相关参数:

* enable.auto.commit:是否开启自动提交offset功能，默认是true
    消费者会自动周期性地向服务器提交偏移量。
* auto.commit.interval.ms:自动提交offset的时间间隔，默认是5s
    默认值为 true，消费者会自动周期性地向服务器提交偏移量。
    如果设置了 enable.auto.commit 的值为 true， 则该值定义了消 费者偏移量向 Kafka 提交的频率，默认 5s。

## 3.手动提交 offset

手动提交offset的方法有两种:分别是commitSync(同步提交)和commitAsync(异步提交)。  
两者的相同点是，都会将本次提交的一批数据最高的偏移量提交;  
不同点是，同步提交阻塞当前线程，一直到提交成功，并且会自动失败重试(由不可控因素导致，也会出现提交失败);
而异步提交则没有失败重试机制，故有可能提交失败。

* commitSync(同步提交):必须等待offset提交完毕，再去消费下一批数据。
* commitAsync(异步提交) :发送完提交offset请求后，就开始消费下一批数据了。

## 4.指定 Offset 消费

auto.offset.reset = earliest | latest | none 默认是 latest。  

当 Kafka 中没有初始偏移量(消费者组第一次消费)或服务器上不再存在当前偏移量 时(例如该数据已被删除)，该怎么办?

1. earliest:自动将偏移量重置为最早的偏移量，--from-beginning。  
2. latest(默认值):自动将偏移量重置为最新偏移量。  
3. none:如果未找到消费者组的先前偏移量，则向消费者抛出异常。

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_26.png)  
![kafka_26](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_26.png)

也可以指定时间消费.

## 5.漏消费和重复消费

重复消费:已经消费了数据，但是offset 没提交。  
漏消费:先提交 offset 后消费，有可能会造成数据的漏消费。

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_27.png)  
![kafka_27](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_27.png)

## 6.消费者事务

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_28.png)  
![kafka_28](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_28.png)

## 7.数据挤压

[图片备用地址](https://limingxie.github.io/images/system/kafka/kafka_29.png)  
![kafka_29](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/kafka/kafka_29.png)

**fetch.max.bytes**  
默认Default: 52428800(50m)。消费者获取服务器端一批消息最大的字节数。  
如果服务器端一批次的数据大于该值 (50m)仍然可以拉取回来这批数据，因此，这不是一个绝 对最大值。  
一批次的大小受 message.max.bytes (broker config)or max.message.bytes (topic config)影响。

**max.poll.records**  
一次 poll 拉取数据返回消息的最大条数，默认是 500 条

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
