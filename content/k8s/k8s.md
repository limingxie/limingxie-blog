---
author: "li_mingxie"
title: "【k8s】kubernetes基本的概念"
date: 2020-07-01T07:28:49+08:00
tags: [
    "k8s",
    "kubernetes",
]
categories: [
    "k8s",
    "kubernetes",
]
---

最近抽时间了解Kubernetes。那些网上和官方的材料都讲得很深，很细。<!--more-->  

我平时也用不上这些技术，不需要知道的这么深这么细，  
花这么多少时间去了解，过几天没怎么用还会忘记的...ㅠㅠ  
其实我想知道的是其大致的结构。  

下面我是以笔记的形式见得整理了其结构。希望对刚刚接触的人的了解有所帮助。  

### **1.基本概念**

**◆ Kubernetes 是什么？**  
引用官方的解释：  

>
Kubernetes 是一个可移植的、可扩展的开源平台，用于管理容器化的工作负载和服务，可促进声明式配置和自动化。  
Kubernetes 拥有一个庞大且快速增长的生态系统。Kubernetes 的服务、支持和工具广泛可用。  

以我的理解：  
**Kubernetes(简称k8s)是一个可以操作容器和自动化部署的开源平台。**  
还不理解？ 没关系~  
简单的说手动管理docker container太累，用Kubernetes管理吧~

一个服务器可以有多个docker container，多个服务器形成一个集群。  
下面简单的介绍一下Kubernetes集群的结构。

### **2.Kubernetes集群结构**

[备用图片](https://limingxie.github.io/images/k8s/components-of-kubernetes.png)  
![components-of-kubernetes](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/k8s/components-of-kubernetes.png)
图片来源于:https://kubernetes.io/docs/concepts/overview/components/

[备用图片](https://limingxie.github.io/images/k8s/kubernetes_cluster.png) 
![components-of-kubernetes](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/k8s/kubernetes_cluster.png)
图片来源于:http://omerio.com/2015/12/18/learn-the-kubernetes-key-concepts-in-10-minutes/

其他结构图：
[备用图片1](https://limingxie.github.io/images/k8s/kube-architecture.png) 
[备用图片2](https://limingxie.github.io/images/k8s/Kubernetes.png)


简单的说：  
每个Kubernetes集群都有他自己的master节点。  
这里包含着kube-apiserver，kube-controller-manager，kube-scheduler，etcd。  
看图和名字可以大致的猜到: 

    `kube-apiserver`是通讯枢纽。  
    `kube-controller-manager`是控制器。  
    `kube-scheduler`是调度器。  
    `etcd`是键值数据库。  

普通的节点包含着kubelet, kube-proxy。  

    `kubelet`跟master节点通讯。  
    `kube-proxy`是网络代理。

下面具体的介绍一下比较重要的组件。

### **3.Kubernetes组件**

**◆ Kubernetes Master**  

每个k8s集群里至少需要一个Master节点来负责整个集群的管理和控制，  
所有控制命令都是发给它，它来负责具体的调度和执行。

master节点包含三个进程和一个键值数据库etcd。  
三个进程都运行在集群中的某个节点上，主控组件通常这个节点被称为 master 节点。  
这些进程包括：kube-apiserver、kube-controller-manager 和 kube-scheduler。  

**kube-apiserver:**  

    集群内各个功能模块之间数据交互和通信的中心枢纽。  
    所有从集群到master的通信路径都终止于apiserver。  

**kube-controller-manager:**  

    通过apiserver监控集群的公共状态，并致力于将当前状态转变为期望的状态。  

**kube-scheduler:**  

    负责分配调度Pod到集群内的节点上，它监听kube-apiserver，
    查询还未分配Node的Pod，然后根据调度策略为这些Pod分配节点。

**etcd:**  

    是键值数据库，保存所有集群数据的后台数据库。需要备份计划。  

**◆ Node**  

在 Kubernetes 中，节点(Node)是执行工作的机器，以前叫做 minion。 
根据你的集群环境，节点可以是一个虚拟机或者物理机器。  
每个节点都包含用于运行 pods 的必要服务，并由主控组件管理。  
集群中的每个非 master 节点都运行两个进程：
kubelet: 负责节点的运行状态，监控和管理pod状态，和master节点进行通信。
kube-proxy: 一种网络代理，将 Kubernetes 的网络服务代理到每个节点上。

**◆ Pod**   
Kubernetes的基本调度单元，一个pod一般包含一个或多个容器。  
每个pod都被分配一个唯一的（在集群内的）IP地址。  

**◆ Service**   
服务内容与具体的 pod 分离。Kubernetes 服务代理负责自动将服务请求分发到正确的 pod 处，不管 pod 移动到集群中的什么位置，甚至可以被替换掉。

**◆ kubectl**  
这是 Kubernetes 的命令行配置工具。用户通过它传输命令。

**◆ Lable**   
后续整理

**◆ Volumes**  
后续整理


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`