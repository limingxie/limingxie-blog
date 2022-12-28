---
author: "li_mingxie"
title: "【区块链笔记】BTC_脚本(07)"
date: 2022-10-26T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
    "脚本",
]
categories: [
    "blockchain",
    "btc",
]
---

这篇文章简单的整理了`比特币脚本`相关的内容。  <!--more-->  
  
## 1.交易的数据结构

先看看比特币交易的数据结构。  

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchan_btc_tx.png)  
![blockchan_btc_tx](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchan_btc_tx.png)

其中需要解释的字段是:  
**locktime**：是用来表明生效的时间的，绝大多数情况下都是0，如果非0的话一般要过一段时间才生效。  
`vin`被称作输入脚本，`vout`被称为输出脚本。

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_input.png)  
![blockchain_btc_input](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_input.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_output.png)  
![blockchain_btc_output](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_output.png)

## 2.脚本的验证

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_script.png)  
![blockchain_btc_script](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_script.png)

### 常用的几个命令

1. OP_DUP   (duplicate) 复制栈顶元素
2. OP_HASH160   (hash) 0xa9 取出栈顶元素，先用SHA-256，再用RIPEMD-160，然后压入栈内。
3. OP_EQUAL (equal) 0x87 如果输入的两个数相等，返回1，否则返回0
4. OP_EQUALVERIFY   (equal verify) 0x88 与OP_EQUAL 一样，如结果为0，之后运行OP_VERIFY
5. OP_CHECKSIG  (check signature)  0xac 弹出栈顶的2个元素，用公钥检查输入中的签名，验证该签名是否是拥有该公钥的用户用私钥签署的，如果匹配返回1，不匹配返回0
6. OP_CHECKMULTISIG (check multiple signature) 0xae 多个公钥需要查询多重签名的正确性
7. OP_RETURN    (return) 0x6a 标记交易无效
8. OP_PUSH  下面多少个字节要被压入堆栈
9. OP_TRUR/OP_1 0x51    数字1被压入堆栈
10. OP_FALSE/OP_0    0x00 一个字节空串被压入堆栈

#### P2PK(Pay to Public Key)

这是最简单的验证模式

    P2PK(Pay to Public Key)
    input script:
        PUSHDATA(Sig)
    output script:
        PUSHDATA(PubKey)
        CHECKSIG

    PUSHDATA(Sig)       //签名
    PUSHDATA(PubKey)    //公钥
    CHECKSIG            //验证

#### P2PKH(Pay to Public Key Hash)

这是最常用的验证模式

    P2PKH(Pay to Public Key Hash)
    input script:
        PUSHDATA(Sig)
        PUSHDATA(PubKey)
    output script:
        DUP
        HASH160
        PUSHDATA(PubKeyHash)
        EQUALVERIFY
        CHECKSIG

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_script_execute.png)  
![blockchain_btc_script_execute](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_script_execute.png)

#### P2SH(Pay to Script Hash)

P2SH(Pay to Script Hash) 前期版本比特币系统中是没有的。
后期用软分叉的方式加进去的。

    P2SH(Pay to Script Hash)
    input script:
        ...
        PUSHDATA(Sig)
        ...
        PUSHDATA(serialized redeemScript)
    output script:
        HASH160
        PUSHDATA(redeemScriptHash)
        EQUAL

用P2SH实现P2PK

    redeemScript:
        PUSHDATA(PubKey)
        CHECKSIG
    input script
        PUSHDATA(Sig)
        PUSHDATA(serialized redeemScript)
    output script:
        HASH160
        PUSHDATA(redeemScriptHash)
        EQUAL

## 3.多重签名

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchanblockchain_btc_muti_sig_btc_tx.png)  
![blockchain_btc_muti_sig](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_muti_sig.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_multi_sig_exec.png)  
![blockchain_btc_multi_sig_exec](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_multi_sig_exec.png)

目前使用的多重签名的方式。  
好处是使用者不用管每个商家的多重签名使用方式，只要输入改脚本的hash值就可以。

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_multi_sig_02.png)  
![blockchain_btc_multi_sig_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_multi_sig_02.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_multi_exec_02.png)  
![blockchain_btc_multi_exec_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_multi_exec_02.png)

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_multi_exec_03.png)  
![blockchain_btc_multi_exec_03](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_multi_exec_03.png)

## 4.Proof of Burn

脚本遇到`RETURN`就会失败。  
存在这样的脚本的原因是，花费一定的费用想在区块链中做记录。  
这个交易是输出脚本，所以写入的时候是不会被执行验证的。  
只有想花费的时候当做输出脚本做验证。

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_btc_script_return.png)  
![blockchain_btc_script_return](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_btc_script_return.png)

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
