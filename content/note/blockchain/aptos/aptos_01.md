---
author: "li_mingxie"
title: "【aptos-笔记】Account账户管理"
date: 2023-10-07T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "aptos",
]
categories: [
    "blockchain",
    "aptos",
]
---

看aptos介绍的时候看到了比较吸引人的一个功能，  
账户恢复和私钥轮换协议。  
好奇是什么原理，查看了aptos官方文档，简单的整理了一下。
<!--more-->  




## 1.Create Account

首先看一下账户创建的过程。

1. 首先生成一个新的公私钥对。  
2. 从用户处获取帐户的首选签名方案:如果帐户应该使用单个签名，或者应该需要多个签名来签署事务。  
3. 根据PubKey和用户签名方式生成一个32字节的authentication key(auth_key)。  
4. 创建一个sequence number为0。auth_key和sequence number存储在account的resource中。  
5. 通过auth_key创建32byte的address。  

Aptos区块链上的每个账户由32字节标识。  
它们通常显示为64个十六进制字符。  
同一个 address 不同的表现方式：  

```bash
Dan: 0000357ea5c1a4e7bc11b2b17ff2dc2dcca69750bfef1e1ebcaccf8c8018175b 
Dan: 0x0000357ea5c1a4e7bc11b2b17ff2dc2dcca69750bfef1e1ebcaccf8c8018175b
Dan: 0x357ea5c1a4e7bc11b2b17ff2dc2dcca69750bfef1e1ebcaccf8c8018175b
```

## 2.Account Types

#### Standard account  

这是标准帐户，对应一个地址和一对相应的公钥/私钥

#### Resource account  

没有相应私钥的自治帐户，开发人员使用该帐户在链上存储资源或发布模块。

## 3.签名方式Signature schemes

目前支持多签:  
* Ed25519: single signature transactions  
* MultiEd25519: multi-signature transactions.  

### 3.1 身份验证密钥（authentication key）

Aptos区块链支持单签和多签账户。多签账户允许多个用户管理共同管理账户资产。  
这时多签账户是不能使用单个公钥代表该账户。  
因此，我们需要一个代表此账号的密钥，这个代表此账户的没有就是所谓的身份验证密钥。  
密钥轮换后，身份验证密钥会发生变化，**但是账户地址不会改变。**  
所以只有最初的身份验证密钥和账户地址是一样的。  

### 3.2 Single-signer的authentication key

1. 新生成 (privkey_A, pubkey_A)
2. 生成auth_key

```
//0x00 => single-signature scheme identifier
auth_key = sha3-256(pubkey_A | 0x00)
```

### 3.3 Multi-signer的authentication key

创建 K-of-N 的多签账户
1. 新生成 N个 ed25519的 public key（p_1, ..., p_n）
2. 生成auth_key

```
//K => K-of-N的 K
//0x01 => multi-signature scheme identifier
auth_key = sha3-256(p_1 | . . . | p_n | K | 0x01)
```

## 4.密钥轮换Rotating the keys

我最关心的内容...^^

上述账户address地址和auth_key身份验证密钥是分离的，  
可以修改auth_key和对应私钥，账户地址address是不会有变化。  

当私钥泄露时，可以从新生成auth_key和私钥，用新的auth_key替换原来的auth_key。  
这样可以实现账户地址不会变化的前提下修改了私钥。

详细的可以 account.move 代码。

```rust
public entry fun rotate_authentication_key(
        account: &signer,
        from_scheme: u8,
        from_public_key_bytes: vector<u8>,
        to_scheme: u8,
        to_public_key_bytes: vector<u8>,
        cap_rotate_key: vector<u8>,
        cap_update_table: vector<u8>,
    ) acquires Account, OriginatingAddress {
        ... ...
    }
```

上述方法是身份验证密钥轮换功能，要授权轮换，我们需要两个签名:  
`cap_rotate_key`：是目前账户的签名  
`cap_update_table`：是新密钥的签名  

使用from_scheme 和from_public_key_bytes验证cap_rotate_key  
使用to_scheme和to_public_key_bytes验证cap_update_table  

> 使用轮换功能，泄露或是丢失其中一个私钥，也可以不改变账户地址的前提下修改账户的私钥。  
> 单签是无法实现这个功能的。

----------------------------------------------

## 附加内容resource

aptos里有resource的概念。  
一个链上的状态是由resource和module组成。  
而这些信息是存储在各个account帐户。  

举一个简单的例子，有如下状态值：  
0x1234::coin::Coin<0x1234::coin::SomeCoin>  

```rust
module 0x1234::coin {
    struct CoinStore<phantom CoinType> has key {
        coin: Coin<CoinType>,
    }

    struct SomeCoin { }
}
```

其中0x1234是账户地址，coin是模块，coin是资源存储的结构体，SomeCoin是一个结构体。


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
