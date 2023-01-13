---
author: "li_mingxie"
title: "【区块链笔记】ETH_web3常用API(02)"
date: 2022-12-01T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
    "eth",
    "web3",
]
---


这篇文章简单的整理了`以太坊web3常用API`相关的内容。  <!--more-->  

## 1.基本信息查询

#### 查看 web3 版本

* v0.2x.x:web3.version.api
* v1.0.0:web3.version

#### 查看 web3 连接到的节点版本(clientVersion)

* 同步: web3.version.node
* 异步: web3.version.getNode((error,result)=>{console.log(result)})
* v1.0.0:web3.eth.getNodeInfo().then(console.log)

## 2. 基本信息查询

#### 获取 network id

* 同步:web3.version.network
* 异步:web3.version.getNetwork((err,res)=>{console.log(res)})
* v1.0.0:web3.eth.net.getId().then(console.log)

#### 获取节点的以太坊协议版本

* 同步:web3.version.ethereum
* 异步:web3.version.getEthereum((err,res)=>{console.log(res)})
* v1.0.0:web3.eth.getProtocolVersion().then(console.log)

## 3.网络状态查询

#### 是否有节点连接/监听，返回true/false

* 同步:web3.isConnect() 或者 web3.net.listening
* 异步:web3.net.getListening((err,res)=>console.log(res))
* v1.0.0:web3.eth.net.isListening().then(console.log)

#### 查看当前连接的peer节点

* 同步:web3.net.peerCount
* 异步:web3.net.getPeerCount((err,res)=>console.log(res))
* v1.0.0:web3.eth.net.getPeerCount().then(console.log)

## 4.Provider

#### 查看当前设置的 web3 provider

* web3.currentProvider

#### 查看浏览器环境设置的 web3 provider(v1.0.0)

* web3.givenProvider

#### 设置 provider

* web3.setProvider(provider)
    web3.setProvider(new web3.providers.HttpProvider('http://localhost:8545'))

## 5.web3 通用工具方法

#### 以太单位转换

* web3.fromWei web3.toWei

#### 数据类型转换

* web3.toString web3.toDecimal web3.toBigNumber

#### 字符编码转换

* web3.toHex web3.toAscii web3.toUtf8 web3.fromUtf8

#### 地址相关

* web3.isAddress web3.toChecksumAddress

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
