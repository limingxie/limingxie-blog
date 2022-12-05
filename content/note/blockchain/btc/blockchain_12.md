---
author: "li_mingxie"
title: "【区块链笔记】BTC_Merkle root hash计算(12)"
date: 2022-11-05T23:28:49+08:00
tags: [
    "区块链",
    "blockchain",
    "BTC",
    "bitcoin",
]
categories: [
    "blockchain",
]
---

今天看了有关算`默克尔根哈希的算法`，简单的整理了一下。  <!--more-->  
  
## 1.Sample区块

我找了一个98801区块的数据，人为的计算一次默克尔根哈希。

[图片备用地址](https://limingxie.github.io/images/blockchain/bitcoin/blockchain_merkle_hash_01.png)  
![blockchain_merkle_hash_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/blockchain/bitcoin/blockchain_merkle_hash_01.png)  

**Merkle root hash:**  

    211ba04dc9883493cbba1618160bff4cb3dd5fe44f1057f2d5b4553fc0f80181

**coinbase hash:**  

    1cd8352f53e0d8e8914e7c4e2509c5648288390fc74c207c1b5482919a783def

**tx1 hash:**  

    4006d7cad120309f6bab70e45319ee8b519eb63c4e91bb7972ce5a052471b848

**tx2 hash:**  

    4e8782354afc43152b48177af49eb971acd1e209384bf816c9380dc47e60fff2

**tx3 hash:**  

    3f4bb8454d44b4bdd48cad4a17c32757f8b432469d815a35a4ae4a789cdcf2bb

## 2.计算步骤

### 第 1 步骤：大小端转换

这一步骤很多地方没提到，所以一开始就不行了...^^;;  
因为计算机计算是反方向计算的，所以第一步先做大小端转换(16进制)

**coinbase hash:**  

    1cd8352f53e0d8e8914e7c4e2509c5648288390fc74c207c1b5482919a783def  
    大小端转换 =>  
    ef3d789a9182541b7c204cc70f39888264c509254e7c4e91e8d8e0532f35d81c  

**tx1 hash:**  

    4006d7cad120309f6bab70e45319ee8b519eb63c4e91bb7972ce5a052471b848  
    大小端转换 =>  
    48b87124055ace7279bb914e3cb69e518bee1953e470ab6b9f3020d1cad70640  

**tx2 hash:**  

    4e8782354afc43152b48177af49eb971acd1e209384bf816c9380dc47e60fff2  
    大小端转换 =>  
    f2ff607ec40d38c916f84b3809e2d1ac71b99ef47a17482b1543fc4a3582874e  

**tx3 hash:**  

    3f4bb8454d44b4bdd48cad4a17c32757f8b432469d815a35a4ae4a789cdcf2bb  
    大小端转换 =>  
    bbf2dc9c784aaea4355a819d4632b4f85727c3174aad8cd4bdb4444d45b84b3f  

#### 第 2 步骤：两两拼接hash值，计算hash值

这句话看着有点绕，其实就是2个hash合成一个hash  

**coinbase hash + tx1 hash:**  

    ef3d789a9182541b7c204cc70f39888264c509254e7c4e91e8d8e0532f35d81c + 48b87124055ace7279bb914e3cb69e518bee1953e470ab6b9f3020d1cad70640  
    Binary hash(Hex bytes) =>  
    d55cb16c4ca242f28f3f6a1ac26eee3e006fd1a093604d127d5c4d8581fea743  
    Binary hash(Hex bytes) =>  
    8e65b7977a579f811f2bb9c3584565cac2b18b19d95c8edb708c5b401b2ee9cd  

**tx2 hash + tx2 hash:**  

    f2ff607ec40d38c916f84b3809e2d1ac71b99ef47a17482b1543fc4a3582874e + bbf2dc9c784aaea4355a819d4632b4f85727c3174aad8cd4bdb4444d45b84b3f  
    Binary hash(Hex bytes) =>  
    b1f17c996b552107e9511d922020b71788906dcfe0114f935f4e73446b5db8eb  
    Binary hash(Hex bytes) =>  
    003ce801f164a1aaafbdc7c196bf1686be3ae61a7c1e9ae07d2ebcbdd9e623ef  

#### 第 3 步骤：持续的两两拼接hash，计算hash值循环这一过程到出最后一个hash值

    8e65b7977a579f811f2bb9c3584565cac2b18b19d95c8edb708c5b401b2ee9cd + 003ce801f164a1aaafbdc7c196bf1686be3ae61a7c1e9ae07d2ebcbdd9e623ef  
    Binary hash(Hex bytes) =>  
    743cf0cdb30bfc86a9b6c1f3855f9ad4abd8681515aef15ae73f8e9465c5ebb0  
    Binary hash(Hex bytes) =>  
    8101f8c03f55b4d5f257104fe45fddb34cff0b161816bacb933488c94da01b21  

#### 第 4 步骤：出最后一个hash值后，做大小端转换

    8101f8c03f55b4d5f257104fe45fddb34cff0b161816bacb933488c94da01b21  
    大小端转换 =>  
    211ba04dc9883493cbba1618160bff4cb3dd5fe44f1057f2d5b4553fc0f80181

最后做大小端转换后得到的就是上面区块的默克尔根哈希。

## 3.模拟代码

用go代码简单的模拟了一下。

```go
package gohash

import (
 "crypto/sha256"
 "encoding/hex"
 "fmt"
)

func getMerkleRootHash() string {
 //先声明4个交易单的hash
 coinBase := "1cd8352f53e0d8e8914e7c4e2509c5648288390fc74c207c1b5482919a783def"
 tx1 := "4006d7cad120309f6bab70e45319ee8b519eb63c4e91bb7972ce5a052471b848"
 tx2 := "4e8782354afc43152b48177af49eb971acd1e209384bf816c9380dc47e60fff2"
 tx3 := "3f4bb8454d44b4bdd48cad4a17c32757f8b432469d815a35a4ae4a789cdcf2bb"

 //大小端转换处理
 r_coinbase := ReverseStr2ByteStr(coinBase)
 r_tx1 := ReverseStr2ByteStr(tx1)
 r_tx2 := ReverseStr2ByteStr(tx2)
 r_tx3 := ReverseStr2ByteStr(tx3)

 //两两计算hash

 //计算hash先转arr
 r_coinbase_tx1_arr, err := hex.DecodeString(r_coinbase + r_tx1)
 if err != nil {
  fmt.Println(err)
 }

 //每次hash计算都是2次
 res_c1 := sha256.Sum256(r_coinbase_tx1_arr)
 res_c1 = sha256.Sum256(res_c1[:])

 r_tx2_tx3_arr, err := hex.DecodeString(r_tx2 + r_tx3)
 if err != nil {
  fmt.Println(err)
 }

 //每次hash计算都是2次
 res_34 := sha256.Sum256(r_tx2_tx3_arr)
 res_34 = sha256.Sum256(res_34[:])

 //字节数组转换成字符相加
 r_res_c123_str := hex.EncodeToString(res_c1[:]) + hex.EncodeToString(res_34[:])

 //两两再一次计算hash
 r_res_c123_arr, err := hex.DecodeString(r_res_c123_str)
 if err != nil {
  fmt.Println(err)
 }

 //2次hash计算
 result := sha256.Sum256(r_res_c123_arr)
 result = sha256.Sum256(result[:])

 //最后一个hash再做一次大小端转换。
 merkleRootHash := ReverseStr2ByteStr(hex.EncodeToString(result[:]))

 return merkleRootHash
}

func ReverseStr2ByteStr(str string) string {
 result := ""
 for i := len(str) / 2; i > 0; i-- {
  result += str[i*2-2 : i*2]
 }
 return result
}

func MainGoMerkleRootHash() {
 fmt.Println(getMerkleRootHash())  //211ba04dc9883493cbba1618160bff4cb3dd5fe44f1057f2d5b4553fc0f80181
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
