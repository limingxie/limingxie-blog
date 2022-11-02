---
author: "li_mingxie"
title: "【区块链笔记】BTC_数据结构(02)"
date: 2022-10-21T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
    "加密",
    "解密",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`比特币的数据结构`相关内容。  <!--more-->  
为了更好的理解比特币的结构，我们需要先了解 hash pointer 和 merkle tree。  

## 1.哈希指针(Hash Pointer)

> 哈希指针 = 数据指针 + 数据hash值。  

看下图:  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/hash_pointer.png)  
![hash_pointer](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/hash_pointer.png)

#### 哈希指针有什么用处？  

tamper-evident log(防篡改日志)  
通过指针找到相对应的数据块后，求hash值和哈希指针的哈希值做对比。  
就很容易校验数据块的合法性。  
因这个特性，我们可以只存后面几个区块信息，通过前一个区块哈希指针，  
可以求出上一块的区块信息，以及校验。  

----------------------------------------------

## 2.Merkle tree

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/merkle_tree_01.png)  
![blockchain_header_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/merkle_tree_01.png)

如上图计算比特币区块中的所有的交易单的hasn值，  
然后每2个再次计算hash值，直到计算出一个root hash值。  

```
H1 = Hash(tx_1)
H2 = Hash(tx_2)
H() = Hash(H1 + H2)
```

下图是轻节点(比如手机端:只会存储头部信息)如何验证一笔交易是合法有效的交易：  
因为hash值的特性，我们只要请求红色的hash值在本地计算出merkle root hash，  
和自己的表头比较就行了。也称之为Proof of Membership。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/merkle_tree_02.png)  
![blockchain_header_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/merkle_tree_02.png)
$$\begin{matrix}
MerkleProof
\end{matrix}
$$

----------------------------------------------

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/merkle_tree_03.png)  
![blockchain_header_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/merkle_tree_03.png)
$$\begin{matrix}
MerkleSortTree
\end{matrix}
$$

----------------------------------------------

## 3.比特币头部结构(Bitcoin Block Headers)

比特币的头部由以下部分组成。  

#### 3.1 版本号(version)

#### 3.2 前一个区块哈希指针(previous block header hash)

比特币的区块存前一个区块的`哈希指针`。  

1. hash值只算前节点的header的hash,因为header里包含着root hash。  
2. 只有后面的区块知道自己的前一个区块是谁，  
    前面的区块是不可能知道自己的后面的区块是谁。  
    需要注意一下指针的方向，比特币白皮书里画的箭头是往后的，这地方很容易产生误解。  
    是后面的区块引用了前面的区块。  

#### 3.3 当前区块哈希(Merkle root hash)

是SHA256值。  
区块中coinbase(挖矿奖励金)的交易单TXID会放在第一位。  
block中任何一个交易单打改变会影响Merkle root hash值  

#### 3.4 时间戳(time)

必须大于前11个区块的中间时间。  
全节点bu允许2个小时前的区块进入。  

#### 3.5 难度值(nBits)

"旷工采矿"打包区块的时候计算hash的难度值。  
大致是中本聪开采的创始区块的hash值，通过这个难度值计算出新的hash值。  
当小于这个hash值时，就可以打包区块获取奖励金了。  

#### 3.6 随机数(nonce)

所有区块的数据准备好以后，调整这个随机数，计算出hash值。  
直到小于通过难度值算出的hash值。算采矿成功。  

下面是比特币官网的解释。  
参考网址：<https://developer.bitcoin.org/reference/block_chain.html>

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_header_struct.png)  
![blockchain_header_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_header_struct.png)

**看看实际数据是如何保存的。**  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_header_data.png)  
![blockchain_header_data](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_header_data.png)

## 4.整体链式结构的组成

全节点(full node): 包含所有的交易内容  
轻节点(light node): 只保存头部信息，所以无法独立验证交易，比如手机客户端  

如上述说的头部信息和body的交易信息构成一个区块链。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_struct.png)  
![blockchain_header_data](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_struct.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
