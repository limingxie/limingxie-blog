---
author: "li_mingxie"
title: "【区块链】以太坊2.0权益证明_信标链(02)"
date: 2099-12-18T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "ETH",
    "ethereum",
    "white paper",
]
categories: [
    "eth",
    "blockchain",
]
---

上一篇文章[【区块链】以太坊2.0权益证明_共识(01)](https://limingxie.github.io/blockchain/eth/eth_2_0/ethereum_pos_1/)  
介绍了以太坊2.0共识相关的基本概念,这一篇详细的整理信标链相关的内容。  <!--more-->  

信标链和权益证明有个比较核心的概念需要知道。  

## 1.时段Epoch, 时隙Slot

信标链beacon chain把出块时间设为12秒。  
这个一段叫做`时隙slot`。 大部分情况下一个slot会出一个区块block。  
32个slot组成`时段epoch`。  
每个周期也就是每个时段Epoch包含32个slot。  
每个时隙slot为12秒，总计6.4分钟。  


[图片备用地址](https://limingxie.github.io/images/blockchain/eth_2_0/Beacon_Chain_Slots_Epochs.png.webp)  
![Beacon_Chain_Slots_Epochs](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/eth_2_0/Beacon_Chain_Slots_Epochs.png.webp)

下图是beaconchain网站的epoch和slot的数据。  

[图片备用地址](https://limingxie.github.io/images/blockchain/eth_2_0/beaconscan.png)  
![beaconscan](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/eth_2_0/beaconscan.png)

## 2.验证者validator

要想成为验证者参与治理，用户必须先**质押32个以太币ETH**。  
运行三种独立的软件：执行客户端(例如geth)、共识客户端和验证器(例如prysm)。  
质押32个以太币会进入激活队列，限制新验证者加入网络的速度。  
是因为避免快速激活许多验证者来攻击系统。  

激活后，验证者将从以太坊网络上的对等节点接收新区块。  
区块中的交易会被重新执行，检查以太坊状态的更改是否有效，验证区块的签名等等...  
之后验证者在整个网络上发该区块的投票（认证attestation）。

当一个验证者的余额达到16ETH时，会被强制退出。  
验证者参与2048个epoch(约9天)治理后，可以“自愿退出”。  
自愿或强制退出的情况下，验证者想要赎回自己的以太币，需要等待4个时段epoch。  
这期间如果发现违规行为，也会罚没slashed。  

一个诚实的验证者的余额可以在大约27小时内提取。  
但是一个被削减的验证器会导致8,192个epoch(大约36天)的延迟。  

下图是验证者生命周期。  
[图片备用地址](https://limingxie.github.io/images/blockchain/eth_2_0/Beacon_Chain_Validator_Lifecycle.png.webp)  
![Beacon_Chain_Validator_Lifecycle](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/eth_2_0/Beacon_Chain_Validator_Lifecycle.png.webp)

## 3.验证者角色

|角色|职能|描述|
|---|---|----|
|委员会成员committee|SubmitAttestation|为该slot的区块进行证明|
|提案人proposer|ProposeBlock|负责生产区块|
|聚合器aggregator|SubmitAggregateAndProof|聚合某个委员会的签名|
|同步委员会SyncCommittee|SubmitSyncCommitteeMessage|发布最新区块的同步消息，为轻节点服务|
|数据同步贡献证明|SubmitSignedContributionAndProof|聚合同步证明消息，并生成贡献证明|

## 4.委员会committee

多个验证者validator组成委员会committee。  
信标链在每个slot期间随机选择的委员会committee，对提议的区块进行证明(attest)。  
(每个委员会至少要有128名验证者)  
为了安全起见，验证器委员会在每个时段epoch都会重组。  

详细的关系如下：  
* 在每个时段epoch，每个验证者分配到一个committee，  
* 一个committee只投一个slot。  
* 一个slot会有多个committee来认证。  

如图：  
[图片备用地址](https://limingxie.github.io/images/blockchain/eth_2_0/Beacon_Chain_RANDAO.png.webp)  
![Beacon_Chain_RANDAO](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/eth_2_0/Beacon_Chain_RANDAO.png.webp)

我看了第7952860个slot 有64个committee，  
每个committee大概有400+个Attestation。  
一个周期epoch是32个slot  
可以算出 400+ * 64 * 32 大约8,90万的验证者参与了投票。  

## 5.同步委员会sync committee

同步委员会有512个验证者组成，一轮持续256 epochs，在整个周期中为最新区块（整条链的header）投票8192次。同步委员会成员会在每个slot的第一时间把自己看到的最新的区块信息进行签名，并广播同步消息到同步子网，同步聚合器会聚合相同投票最多的同步进行并进行广播。

当前和下一轮同步委员（current sync committee, next Sync committee）会可以提前很久得知， 轻节点很容易从finality的区块状态中验证这一点，一旦委员会的身份被确认，剩下的问题主要是验证委员会的投票结果。

最新的区块中已经包含了同步委员会对最新区块的投票结果，512个成员是否投票以bitfield的形式记录，验证者可以根据参与投票者的公钥，聚合后的签名验证同步证明数据的有效性。然后根据投票百分比来判定最新区块的可靠性。不过，这仍旧是一种基于信任的解决方案，在前文以太坊路线图解读中讨论过未来的以太坊无状态客户端不再是基于信任的而是基于验证的，轻节点可以自己验证最新区块的合法性。

## 6.检查点Checkpoints

每个epoch的第一个slot的块block是检查点Checkpoint。  
如果没有这样的块，那么检查点就是前面最近的块。  
每个epoch都会有一个检查点块。一个块可以是多个epoch的检查点。

如下图假如65 ~ 128slot空的。  
Epoch 2的检查点应该是128号slot的方块。由于slot的缺失，所以Epoch 2检查点是slot 64上的块。
Epoch 3也是Slot 192是空的，因此Slot 180的块是Epoch 3的检查点。

[图片备用地址](https://limingxie.github.io/images/blockchain/eth_2_0/Beacon_Chain_Checkpoints.jpg.webp)  
![Beacon_Chain_Checkpoints](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/eth_2_0/Beacon_Chain_Checkpoints.jpg.webp)

在进行LMD GHOST投票(区块验证)时，验证者也对其当前epoch(叫target)中的检查点进行投票。  
这种投票称为Casper FFG投票，还包括前一个epoch(叫source)的检查点。  

## 7.认证attestation

[图片备用地址](https://limingxie.github.io/images/blockchain/eth_2_0/Beacon_Chain_Validators.png.webp)  
![Beacon_Chain_Validators](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/eth_2_0/Beacon_Chain_Validators.png.webp)

所有有效验证者的总余额中有三分之二的投票被视为绝对多数。教学上，假设有三个活动验证器:两个有8 ETH的余额，一个唯一的验证器有32 ETH的余额。超级多数投票必须包含唯一验证者的投票:尽管其他两个验证者对唯一验证者的投票可能不同，但他们没有足够的平衡来形成超级多数。

## 8.聚合者（aggregator）

聚合器，负责将某个委员会成员的证明签名聚合成一个签名，然后再向全网广播，这将大大减少网络带宽和存储资源的消耗。

聚合器负责将未聚合的投票消息进行聚合。每个slot会有数以万计的验证者参与到共识投票过程，他们的vote数据并不会直接进行全网广播，而是在子网内传播，然后由子网内的聚合器进行聚合成一个聚合证明数据后再广播到整个网络。而区块中包含的是聚合后的证明数据。

聚合器的身份只跟验证者自己公钥和所在slot有直接关系，验证者对（公钥pk+slot）签名，签名后哈希第一个字节作为选择的种子，然后对modulo取余，等于0则被选中。此外，所在委员会大小也会影响聚合身份的比率，可以粗略的认为每10个人会产生一个聚合器，因此一个委员会会有多个聚合器存在，它们各自负责为自己的子网进行签名聚合。聚合器被分配到哪个子网是随机的。

对聚合结果签名，全网广播聚合结果和证明。

proposer在组装区块是会根据slot，blockroot的数据，对聚合数据进行选择，选出slot中最好的各个聚合签名（包含的证明越多越好），然后将其包含在区块中。

一个attestation的生命周期包含5个阶段：
验证者生成投票证明（generation），
将证明广播到自己所在的subnet子网（propagation），
subnet中的聚合器负责对这些证明进行聚合（aggregation），
将聚合后数据广播到整个网络（propagation），
proposer将聚合后的证明包含到区块当中（inclusion）。

<https://beaconcha.in/slot/6240943#votes>

这里是全部的投票数据，所有参与投票的验证者ID，一共9158次投票。

<https://beaconcha.in/slot/6240943#attestations>

这里是全部的聚合后数据，9158次投票被聚合成128个聚合证明。


Attestation，SyncCommittee两种消息，最初并不会进行全网广播，而是先广播到所在的子网（subnet），子网中的聚合器（Aggregator）负责对这些初始的未聚合的消息进行聚合，子网基于同一slot和committee，因此，可以认为某个子网的验证者在对同样的数据进行证明，这样，所有的未聚合数据就可以聚合成一个聚合后证明，聚合器对聚合结果签名后将聚合后证明广播到全网。proposer再把各个聚合后的数据包含到区块里。

通过子网的划分，实现验证者角色和数据的划分：可以防止聚合数据相互包含，每个验证者在每个epoch只会参与一次证明；子网相当于是一个独立订阅主题的通道（topic= "/eth2/forkid/beacon_attestation_subnetid"），通过定向广播数据，降低网络负载；

验证者有对应子网的职责，就会订阅对应子网的数据。未聚合的原始证明数据和子网是基于slot和committee分组的。每个epoch由32个slot和64个committee，他们构成了一个32*64网格，所有的验证者都被分配到对应的网格里，这个网格就代表一个subnet，代表在指定slot的某个委员会负责为该slot证明的成员集合，因此，也可以认为每个slot都有64个小子网共同对slot数据进行证明，他们各自的证明被进行聚合。

## 9.最终确定性Finality

## 10.分片链Shard Chains

每个时段epoch(6.4 分钟)验证者都会向网络提议一个认证。这个认证是针对时段中的一个特定时隙slot。  
认证的目的是投票赞成验证者对于链的看法，特别是最近的合理区块和当前时段的第一个区块（被称为来源和目标检查点）。 所有参与的验证者的信息都会合并，使得网络可以达成关于区块链状态的共识。

认证是验证者对于链的状态和区块的投票，它们有助于保持网络的安全和活跃。每个时段（6.4 分钟），验证者会被分配到一个委员会，每个委员会对应一个子网。验证者会在子网上广播他们的认证，然后由一个聚合者收集并聚合这些认证，最后由一个区块提议者将聚合的认证包含到新的区块中。1

聚合者Aggregator

在信标链与最初的以太坊主网合并后，以太坊社区开始寻求扩展该网络。
权益证明的优势是，在任何给定时间都有全部已批准的区块生产者的记录，每个区块生产者都质押了以太币。  
这个记录不但为分开治理奠定了基础，还可靠地划分了具体的网络责任。
这种责任与工作量证明形成对比，在工作量证明中，矿工对网络没有义务，可以立即停止挖矿并永久关闭其节点而不会受到任何影响。  
而且，也没有已知区块提议者的记录，并且没有可靠的方法安全地划分网络责任。

只有在已建立权益证明共识机制的情况下，分片才能安全进入以太坊生态系统。  
信标链引入了质押，它与主网“合并”，为分片铺平了道路，以帮助进一步扩展以太坊。

在工作量证明中，生成区块的时间是由挖矿难度决定的，而在权益证明中，节奏是固定的。 权益证明以太坊中的时间分为时隙（12 秒）和时段（32 个时隙）。 在每个时隙中随机选择一位验证者作为区块提议者。 该验证者负责创建新区块并发送给网络上的其他节点。 另外在每个时隙中，都会随机选择一个验证者委员会，通过他们的投票确定所提议区块的有效性。 将验证者集合划分为若干个委员会对于保持网络负荷易于管理非常重要。 委员会将验证者集合分成不同部分，以便每个活跃的验证者在每个时段都会出示证明，但并不在每个时隙都这样做。
验证者需要在每个时段epoch创建、签名和广播认证。

每个时隙都会以伪随机的方式选择一个验证者来提议区块，使用的算法被称为 RANDAO，它将区块提议者的哈希与一个随每个区块更新的种子混合在一起。 这个值用于在所有验证者中选择一个特定的验证者。 验证者的选择会提前四个时段固定。

----------------------------------------------

## PS 汉英翻译

| 中文 | 英文 |
| --- | --- |
|时段|epoch  |
|时隙|slot  |
|检查点|checkpoint  |
|信标链|beacon chain  |
|聚合者|Aggregator  |
|弱主观性|Weak Subjectivity  |
|认证|attestation  |
|分片链|shard chain  |
|分片|shard  |
|验证者|validator  |


**参考**  

<https://ethereum.org/zh/glossary/>  
<https://ethereumhttps://github.com/ethereum/pos-evolution/blob/master/pos-evolution.md>
<https://ethos.dev/beacon-chain>
<https://zhuanlan.zhihu.com/p/625003066>
⅔

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
