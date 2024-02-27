---
author: "li_mingxie"
title: "【FISCO笔记】证书生成和验证"
date: 2024-02-25T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "fisco",
    "bcos",
]
categories: [
    "fisco",
    "ca",
]
---

FISCO BCOS网络采用面向CA的准入机制，使用**x509协议的证书格式**，  
支持任意多级的证书结构，保障信息保密性、认证性、完整性及不可抵赖性。  
根据现有业务场景，FISCO BCOS默认采用三级的证书结构，自上而下分别为链证书、机构证书、节点证书。  <!--more-->  

x509协议证书包括了证书版本号、序列号、证书的签名算法、消息摘要算法等生成信息；  
同时包括了证书的颁发者、有效期、使用者、公钥信息、SSL通信需要用到的密码套件等信息。

节点通过加载证书，在接受数据包时，根据证书规定的密码套件和其消息字段，对数据包中携带的证书进行验证。

## 1、角色

FISCO BCOS的证书结构中有４种角色，分别是联盟链委员会、联盟链成员机构、联盟链参与方（节点和SDK）。

### 1. 联盟链委员会

联盟链委员会拥有联盟链的根证书ca.crt和私钥ca.key，  
通过使用ca.key对联盟链成员机构签发机构证书，负责完成联盟链成员机构的准入、剔除等操作。 

```console
ca.crt  链证书
ca.key  链私钥
```

### 2. 联盟链成员(机构)

联盟链成员机构为经过联盟链委员会许可，加入到联盟链的机构。  
联盟链成员机构拥有机构私钥agency.key和经过根私钥ca.key签发的机构证书agency.crt。  
联盟链成员机构可以通过机构私钥签发节点证书，从而配置本机构的节点和SDK。  

```console
ca.crt        链证书
agency.crt    机构证书
agency.csr    机构证书请求文件
agency.key    机构私钥
```

### 3. 联盟链参与方(节点/SDK)

联盟链参与方可以通过运行节点或SDK联盟链交互，  
拥有与其他节点进行通信的节点证书node.crt和私钥node.key。  
联盟链参与方运行节点或SDK时，需要加载根证书ca.crt和相应的节点证书node.crt和私钥node.key；  
与其他成员进行网络通信时，使用预先加载的证书进行身份认证。  

```console
ca.crt      链证书
node.crt    节点证书
node.key    节点私钥
sdk.crt     SDK证书
sdk.key     SDK私钥
```

## 2、证书生成流程

### 1.联盟链委员会初始化根证书ca.crt

* 本地生成私钥ca.key；
* 自签生成根证书ca.crt。

[图片备用地址](https://limingxie.github.io/images/blockchain/fisco/ca/ca_01.png)  
![fisco_bcos](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/fisco/ca/ca_01.png)  

### 2.联盟链成员机构获取机构证书agency.crt

* 本地生成私钥agency.key；
* 由本地私钥生成证书请求文件agency.csr；
* 将证书请求文件agency.csr发送至联盟链委员会；
* 联盟链委员会使用ca.key对证书请求文件agency.csr进行签发，得到联盟链成员机构证书agency.crt；
* 联盟链委员会将联盟链成员机构证书agency.crt发送至对应成员。

[图片备用地址](https://limingxie.github.io/images/blockchain/fisco/ca/ca_02.png)  
![fisco_bcos](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/fisco/ca/ca_02.png)  

### ３.节点/SDK获取证书node.crt

* 本地生成私钥node.key；
* 由本地私钥生成证书请求文件node.csr；
* 将证书请求文件node.csr发送至联盟链成员机构；
* 联盟链成员机构使用agency.key对证书请求文件node.csr进行签发，得到节点/SDK证书node.crt；
* 联盟链成员机构将节点证书node.crt发送至对应实体。

[图片备用地址](https://limingxie.github.io/images/blockchain/fisco/ca/ca_03.png)  
![fisco_bcos](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/fisco/ca/ca_03.png)  

## 3、证书验证

1. 在程序启动时，会首先加载本地的ca.crt和node.crt；
2. 当节点验证对方的证书时，会首先使用对方node.crt中的公钥验证附带的签名，当验证通过时，可以确认对应节点拥有当前node.crt对应的node.key；
3. 之后节点会使用agency.crt中的信息验证node.crt是否为合法的agency签发；
4. 最后节点会使用本地加载ca.crt中的信息验证agency.crt是否为联盟链委员会签发；
5. 当两者都验证通过时，表示节点接收到的node.crt是由本地加载的ca.crt签发的。

[图片备用地址](https://limingxie.github.io/images/blockchain/fisco/ca/ca_04.png)  
![fisco_bcos](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/fisco/ca/ca_04.png)  

```console
.key	私钥文件  
.crt	证书文件  
.csr	证书请求文件  
```

## 4、加密算法

[图片备用地址](https://limingxie.github.io/images/blockchain/fisco/ca/ca_05.png)  
![fisco_bcos](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/fisco/ca/ca_05.png)  

## 5、证书生成实例

以生成联盟链成员机构机构证书为例：

1.机构首先在本地使用机构私钥agency.key生成证书请求文件agency.csr；

```console
openssl req -new -sha256 -subj "/CN=$name/O=fisco-bcos/OU=agency" -key ./agency.key -config ./cert.cnf -out ./agency.csr
```

2.联盟链委员会根据证书请求文件生成机构证书agency.crt；

```console
openssl x509 -req -days 3650 -sha256 -CA ./ca.crt -CAkey ./ca.key -CAcreateserial -in ./agency.csr -out ./agency.crt  -extensions v4_req -extfile ./cert.cnf
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
