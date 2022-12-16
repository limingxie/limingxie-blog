---
author: "li_mingxie"
title: "【区块链笔记】ETH_权益证明(07)"
date: 2022-11-14T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
]
categories: [
    "blockchain",
]
---

这篇文章简单的整理了`以太坊的权益证明`相关的内容。  <!--more-->  

## 1.POW机制

比特币和以太坊采用的都是POW机制(今年9月份以太坊刚合并Beacon Chain)，  
但这种方式有很多人说，浪费太多的电力资源。  

据粗略的统计，比特币的每一笔交易都会消耗1000多度电力，而且完矿奖励也逐渐减少(后续会不会有这么多人挖矿呢？)。  
以太坊的POW比比特币稍微好点，但也是很耗电的。  

其实换个角度去想的话，拼算力也就是拼钱(算力需要大量的自己买入设备)。  
那直接拼钱呢？把这些买设备，费电的费用用在系统开发和维护不好吗？这就是权益证明的基本思想。  

## 2.权益证明(POS)

权益证明(POS)是构成共识机制的基础，区块链使用这些机制来实现分布式共识。  
以太坊采用权益证明机制，在该机制下，验证者明确地通过以太币将资本质押到以太坊上的智能合约中。  
这些质押的以太币充当抵押品，如果验证者有失信行为或者消极怠工，那么可以销毁抵押品。  
验证者负责检查在网络上传播的新区块是否有效，并偶尔自己也创建和传播新区块。  

与工作量证明体系相比，权益证明有许多改进：  

* 能效更高 – 无需在工作量证明计算中使用大量能源
* 门槛更低、硬件要求下降 – 无需购买高性能硬件以便获得创建新区块的机会
* 中心化风险降低 – 权益证明应该可以增加保护网络安全的节点
* 由于能源需求低，发行较少的以太币就可以激励大家参与
* 与工作量证明相比，对不当行为的经济处罚让 51% 攻击的代价呈指数级增加。
* 如果 51% 攻击是为了攻破加密经济的防御，那么社区可以求助于诚实链的社会恢复

优点  
权益质押更加去中心化。 规模经济不像适用于工作量证明挖矿那样适用于权益证明。  
权益证明的加密经济安全性高于工作量证明  
需要发行较少的新以太币就可以激励网络参与者  

缺点  
与工作量证明相比，权益证明仍处于起步阶段，并且经过的实践检验较少。  
实现权益证明比实现工作量证明更加复杂。  
用户需要运行三种软件才能参与以太坊的权益证明。  

### 验证者

要想作为验证者参与，用户必须向存款合约存入 32 个以太币并运行三种独立的软件：执行客户端、共识客户端和验证者。  
存入以太币时，用户会进入一个激活队列，限制新验证者加入网络的速度。 激活后，验证者将从以太坊网络上的对等节点接收新区块。  
区块中交付的交易会被重新执行，并且对区块签名进行检查以确保区块是有效的。  
然后验证者在整个网络上发送支持该区块的投票（称为认证）。  

在工作量证明中，生成区块的时间是由挖矿难度决定的，而在权益证明中，节奏是固定的。  
权益证明以太坊中的时间分为时隙（12 秒）和时段（32 个时隙）。  
在每个时隙中随机选择一位验证者作为区块提议者。  
该验证者负责创建新区块并发送给网络上的其他节点。  
另外在每个时隙中，都会随机选择一个验证者委员会，通过他们的投票确定所提议区块的有效性。  

### 最终确定性

交易在分布式网络具有“最终确定性”是指，该交易是区块的一部分且无法改变，除非燃烧掉大量以太币。  
在权益证明以太坊上，通过“检查点”区块来管理确定性。  
每个时段中的第一个区块是检查点。 验证者为他们认为有效的“检查点对”投票。  
如果一对检查点获得了质押以太币总数中三分之二以上的投票，那么这对检查点将被升级。  
这两个检查点中较新的一个会变成“合理”状态。 较旧的一个检查点已经是合理状态，因为它是上一个时段中的“目标”。  
现在，这个检查点会升级为“已确定”状态。 要回滚最终确定的区块，攻击者将承担至少相当于质押以太币总数三分之一的损失。  
此以太坊基金会博文解释了其确切原因。 因为最终确定性需要获得三分之二多数投票，攻击者可以用质押以太币总数的三分之一投票来阻止网络实现最终确定性。  
有一种可以防御这种攻击行为的机制：怠惰惩罚。 当链超过四个时段无法最终确定时，这项机制会触发。  
怠惰惩罚逐渐消耗与大多数投票相反的验证者的质押以太币，使得大多数验证者重新获得三分之二多数投票并最终确定链。  

### 加密经济的安全性

运行验证者是一种承诺。 验证者应当保持足够的硬件和连接，来参与区块的验证和提出。  
作为回报，验证者将获得以太币（他们的质押余额增加）。  
另一方面，作为验证者参与，也为用户为了个人利益或破坏而攻击网络开辟了新的渠道。  
为了防止这种情况，如果验证者在被调用时未能参与，他们就会错过以太币奖励；如果他们有不诚实行为，他们现有的质押可能会被销毁。  
主要有两种行为被视为不诚实：  
在一个时隙中提出多个区块（模棱两可）和提交相互矛盾的认证。  
被罚没以太币的金额取决于大致同一时间受到罚没的验证者数量。  
这称为“相关惩罚”，相关惩罚可以是轻微的（单个验证者被罚没质押以太币的 1%），也可以导致验证者质押的以太币全部被销毁（大额罚没事件）。  
这种惩罚在强制退出期执行，首先是第 1 天的立即处罚（最多 0.5 个以太币），接着是第 18 天的相关惩罚，最后是第 36 天的逐出网络。  
如果验证者在网络上但不提交投票，他们每天都会受到轻微的认证惩罚。  
对于攻击者来说，这些措施都意味着协同攻击的代价将极其高昂。  

### 分叉选择

当网络以最佳状态诚信运行时，链头始终只会有一个新区块并且所有验证者都会证明它。  
然而，由于网络延迟或因为区块提议者提议多个区块（模棱两可），验证者可能看到不同的链头视图。  
因此，共识客户端需要一种算法来确定支持哪一个区块。  
权益证明以太坊中使用的算法称为 LMD-GHOST。它的工作原理是确定其历史记录中具有最大证明权重的分叉。  

### 权益证明和安全性

正如在工作量证明中一样，权益证明中仍然存在 51% 攻击的威胁，但对于攻击者来说风险却更大。  
攻击者需要 51% 的质押以太币。 然后他们可以通过自己的认证确保他们首选的分叉拥有最多累积认证。  
共识客户端使用累积认证的“权重”确定正确的链，所以攻击者能够使他们的分叉成为规范区块。  
然而与工作量证明相比，权益证明的优势在于，社区能够灵活地发动反击。  
例如，诚实的验证者可以决定继续在非主流链上构建并忽略攻击者的分叉，同时鼓励应用程序、交易所和池也这样做。  
他们还可以决定强行将攻击者从网络中移除并销毁攻击者质押的以太币。 这些都是对 51% 攻击的强有力的经济防御措施。  

51% 攻击只是其中一种恶意行为。  
不良行为者可能会尝试远程攻击（尽管最终确定性小工具抵消了这种攻击向量）、  
短程“重组”（尽管提议者权重提升和认证期限可以缓解这种情况）、  
弹跳攻击和平衡攻击（也可以通过提议者权重提升来缓解，  
并且这些攻击只能在理想化的网络条件下演示）或雪崩攻击（被只考虑最新消息的分叉选择算法规则抵消）。  

总的来说，已经证实以太坊实施的权益证明在经济方面比工作量证明更安全。  

质押使个人更容易参与其中保障网络的安全，促进去中心化。 验证者节点可以在普通笔记本电脑上运行。 质押池让用户可以在没有 32 个以太币的情况下质押。

## 3.其他

然EOS加密货币，其采用的是DPOS：Delegated Proof of Stake.  
该协议核心思想是通过投票选21个超级节点，再由超级节点产生区块。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`