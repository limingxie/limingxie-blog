---
author: "li_mingxie"
title: "【配置中心】Apollo和Nacos简介"
date: 2022-11-15T23:28:49+08:00
tags: [
    "Nacos",
    "Apollo",
    "配置中心",
]
categories: [
    "etc",
]
---

这几天因公司需求调研了几个配置中心。  
这里简单的整理了几个配置中心的介绍。  <!--more-->  

## 1.传统应用配置问题

* 主要采用本地文件静态配置  
  \- 本地静态配置导致不能在运行中修改 => `集中管理`
* 配置格式散乱不标准  
  \- 有的用.json , xml, yaml, 数据库存储 => `标准化`
* 容易引发生产事故  
  \- 发布时容易将非生产的配置带过去，引发事故 => `环境隔离`
* 配置修改麻烦，流程长  
  \- 部署不同环境服务器时，修改很费时，也容易出错 => `实时生效`
* 配置信息缺少安全审计和版本控制  
  \- 事后无法追溯，也很难及时回滚 => `版本管理`

有了配置中心可以做`开关驱动开发`  

[图片备用地址](https://limingxie.github.io/images/config/config_01.png)  
![config_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/config_01.png)  

## 2.国内主流的配置中心

**Apollo**  
2016年5月，携程开源的配置管理中心，具备规范的权限、流程治理等特性。  
**Nacos**  
2018年6月，阿里开源的配置中心，也可以做DNS和RPC的服务发现。  
**Spring Cloud Config**  
2014年9月开源，Spring Cloud 生态组件，可以和Spring Cloud体系无缝整合。  
**Disconf**  
2014年7月百度开源的配置管理中心，同样具备配置的管理能力，最近一次是14月前提交代码。  

## 3.Apollo

**Apollo说明文档：**  
<https://www.apolloconfig.com/#/zh/README>

**GitHub：**  
<https://github.com/apolloconfig/apollo>

**源码解析：**  
<https://www.iocoder.cn/categories/Apollo/>

**License：**  
Apache-2.0 license  

演示环境（Demo）:  
<http://106.54.227.205>  
账号/密码:apollo/admin  

#### 3.1 Apollo：核心概念

* 应用(application)  
 \- 实际使用配置的应用 => appid  
* 环境(environment)  
 \- 配置对应的环境：DEV，FAT，UAT，PRO  
* 命名空间(namespace)  
 \- 一个应用下不同配置的分组，应用默认有自己的配置，也可以使用公共组件的配置  
* 集群(cluster)  
 \- 一个应用下不同实例的分组:  
    比如典型的可以按照数据中心分，把上海机房的应用实例分为一个集群，把北京机房的应用实例分为另一个集群  

#### 3.2 Apollo：权限管理

* 系统管理员  
 \- 拥有所有权限
* 创建者  
 \- 可以代为创建项目，责任人是默认的项目管理员
* 项目管理员  
 \- 可以创建Namespace, 集群有管理项目和权限
* 编辑权限  
 \- 只能编辑不能发布
* 发布权限  
 \- 只能发布不能编辑
* 查看，普通用户  
 \- 可以搜索和查看，但不能做其他操作

#### 3.3 Apollo：模块介绍

[图片备用地址](https://limingxie.github.io/images/config/apollo/apollo_config_01.png)  
![apollo_config_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/apollo/apollo_config_01.png)  

#### 3.4 Apollo：架构

[图片备用地址](https://limingxie.github.io/images/config/apollo/apollo_config_02.png)  
![apollo_config_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/apollo/apollo_config_02.png)  

[图片备用地址](https://limingxie.github.io/images/config/apollo/apollo_config_03.png)  
![apollo_config_03](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/apollo/apollo_config_03.png)  

#### 3.5 Apollo： Ctrip 部署图

[图片备用地址](https://limingxie.github.io/images/config/apollo/apollo_config_04.png)  
![apollo_config_04](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/apollo/apollo_config_04.png)  

* Portal部署在生产环境的机房，  
    通过它来直接管理FAT、UAT、PRO等环境的配置  
* Meta Server、Config Service和Admin Service  
    在每个环境都单独部署，使用独立的数据库  
* Meta Server、Config Service和Admin Service  
    在生产环境部署在两个机房，实现双活  
* Meta Server和Config Service部署在同一个JVM进程内，  
    Admin Service部署在同一台服务器的另一个JVM进程内  

#### 3.6 Apollo：开发语言支持

* Java客户端 => 原生支持
* Net客户端 => 原生支持
* Go, Nodejs => 第三方客户端

## 4.Nacos

**Nacos说明文档：**  
<https://nacos.io/zh-cn/>  

**GitHub：**  
<https://github.com/alibaba/nacos>  

**提供go和nodejs的sdk：**
<https://github.com/nacos-group>  

**License：**  
Apache-2.0 license  

#### 4.1 Nacos：数据模型

[图片备用地址](https://limingxie.github.io/images/config/nacos/nacos_config_01.png)  
![nacos_config_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/nacos/nacos_config_01.png?x-oss-process=image/resize,w_500,m_lfit)  

#### 4.2 Nacos：部署架构图

建议在内部隔离网络环境中部署，不建议部署在公共网络环境。

* 单机模式  
 \- 用于测试和单机试用。
* 集群模式  
 \- 用于生产环境，确保高可用。
* 多集群模式  
 \- 用于多数据中a心场景。

[图片备用地址](https://limingxie.github.io/images/config/nacos/nacos_config_02.png)  
![nacos_config_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/nacos/nacos_config_02.png?x-oss-process=image/resize,w_500,m_lfit)  

#### 4.3 Nacos：开发语言支持

java, go, c++, python, nodejs, c#  
<https://github.com/nacos-group>  

## 5.Apollo VS Nacos

[图片备用地址](https://limingxie.github.io/images/config/config_02.png)  
![config_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/config/config_02.png)  

## 6.其他

因为我们主要使用语言是go语言，下面有个新鲜出炉的go语言开发的配置中心。

**Sail说明文档：**  
<https://sail.hyy-yu.space>  

**GitHub：**  
<https://github.com/HYY-yu/Sail>  
  
**License：**  
MIT license  

**演示环境（Demo）:**  
<https://sail-demo.hyy-yu.space/ui/login>  
账号/密码: demo / Demo123  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
