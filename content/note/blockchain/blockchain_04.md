---
author: "li_mingxie"
title: "【区块链笔记】BTC_交易系统(04)"
date: 2022-10-23T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
    "共识协议",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`比特币交易系统`相关内容。  <!--more-->  
比特币是基于交易模式(Transaction based ledger)  
以太坊是基于账户模式(accoubnbt-based ledger)  

## 1.UTXO(Unspent Transaction Output)

未花费交易输出UTXO(Unspent Transaction Output)  

* UTXO是用比特币拥有者的公钥锁定(加密)的一个数字
* 比特币 = UTXO
* 比特币系统里没有比特币，只有UTXO
* 比特币系统里没有账户，只有UTXO(使用公钥锁定)
* 比特币系统里没有账户余额，只有UTXO(账户余额只是比特币钱包的概念)
* UTXO存在权节点的数据库里，为了方便查询检测全节点是在内存当中存储utxo。
* 转账消耗属于自己的UTXO, 同时生成新的UTXO,并用接受者的公钥加密锁定

```
total input = total output
1 BTC = 0.99BTC + 0.01BTC(tx fee)
```

每笔UTXO要记录产生它的交易hash值以及属于第几个交易，  
因为一个交易有可能只有一部是分属于UTXO，因为另一个交易有可能会花掉。  

比特币没有账户的概念，所以转账的时候需要表明你得这个币是从哪里来  
因为通过这些方式去判断每笔交易是否存在双花(double spending)。

下面是比特币交易的详细数据。  
coinbase的第一笔交易和普通交易。  

----------------------------------------------

#### coinbase的交易单

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/btc_coinbase.png)  
![btc_coinbase](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/btc_coinbase.png)

这里有个地方需要说明，现在挖矿的人很多，难度值也很高，  
所以算完整个随机数也有可能找不到满足难度值的答案。  
细看头部信息能改的是merkle root hash  
可以调整coinbase交易的备注值，从新计算满足难度的hash值。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/btc_coinbase_detail.png)  
![btc_coinbase_detail](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/btc_coinbase_detail.png)

----------------------------------------------

#### 普通交易单

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/btc_tx_details.png)  
![btc_tx_details](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/btc_tx_details.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/btc_inputs_outpus.png)  
![btc_inputs_outpus](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/btc_inputs_outpus.png)

普通的交易验证合法性的时候，把输出脚本和输入脚本配对验证，  
注意的是不是同一个交易的两个脚本，而是两个不同交易的脚本。  

----------------------------------------------

#### Block Header 的源码结构

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/block_header_struct.png)  
![block_header_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/block_header_struct.png)

----------------------------------------------

## 2.如何防范恶意节点的攻击

挖矿出块是有出块奖励coninbase + 记账费的，coinbase是没4年大致减少一半，  
后续的话应该是记账费为出块主要的收入来源。  

挖矿其实就是比拼算力，看起来没什么作用，但是挖矿过程是维护这个系统的关键  
挖矿这一过程保证了挖矿的公平性，因为不管你的算力是别人的几倍，挖矿的过程还是那个概率。  
这一个概率不会因你的算力的高低而累加。  

##### 假设有恶意节点，挖矿的时候恶意添加虚假交易怎么办？

比如出块的恶意节点，添加一个虚假的交易，给自己转账？  
这是不可能的，因为他没有别人的私钥。所以根本解不出别人的UTXO。  
这么出块无法通过其他节点的验证，不会被其他诚实节点接受，有可能白白浪费出块奖励。  

##### 一个UTXO花费2次怎么办？

想花费2次需要利用临时分叉, 在不同的链路上花费，  
万一你的交易是在废弃的分叉上做的交易，后续将会废弃掉。  
想防止这种情况的发生需要多等几个区块确认。  
一般比特币是确认6个区块确认, 就可以认为这个交易是有效的了。  
(出一个区块需要大约10分钟，那就是需要等1个小时...^^;;)  

##### 万一有人做selfish mining怎么办？

selfish mining 是怎么做的？  
简答的说就是挖出区块后，先留着不发，让所有人继续算这个区块，自己先算后续区块的随机数。  
这样有人算出来，你可以直接上2个区块，把别人算出来的踢掉得2个区块的奖励。  
想这么做理论上是可以，但是实际上很难实现。因为这样代表你的算力比别人都强很多。  
而且万一后续区块你还没算出来的时候，别人算出该区块的随机数就面临着和你竞争。  
有可能能当前区块的奖励也拿不到。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/selfish_mining.png)  
![selfish_mining](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/selfish_mining.png)

## 3.出块确认

一般很多购物网站也会用到0确认机制。因为有以下两个原因：  

1. 当发出去交易后，大部分节点都是先处理先听到的消息后打包数据出块，  
2. 购物网站从确认到发货本身就需要一些时间，发货是发现没收到钱可以不发货  

当消息太多写不完的时候，会写到下一个模块上，并不会把消息丢弃掉。  
但是你如果给的手续费比较低，有可能一直压着不给你打包到区块的可能性是有的。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_confirmation.png)  
![blockchain_btc_confirmation](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_confirmation.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
