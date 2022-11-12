---
author: "li_mingxie"
title: "【区块链笔记】BTC_模拟区块链代码(14)"
date: 2022-11-07T23:28:49+08:00
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

今天简单的写了`模拟区块链代码`。  <!--more-->  

## 1.具体代码

```go
package main

import (
 "bufio"
 "bytes"
 "crypto/sha256"
 "fmt"
 "os"
 "strconv"
 "time"
)

type Block struct {
 Timestamp     int64  //时间戳
 Data          []byte //挡圈区块存放的信息
 PrevBlockHash []byte //上一个区块的hash
 Hash          []byte //当前区块的hash
}

type BlockChain struct {
 Blocks []*Block
}

func BlockChainStart() {
 b := NewBlockChain()

 //用户输入指令
 cmd := ""

 for {
  fmt.Println("按数字1，添加一条信息到区块链中")
  fmt.Println("按数字2，遍历单签区块链中的信息")
  fmt.Println("按其他按键则退出")
  fmt.Println("----------------------------")

  fmt.Scanf("%s", &cmd)

  switch cmd {
  case "1":
   //添加新区块
   fmt.Println("请输入交易信息：")
   r2 := bufio.NewReader(os.Stdin)
   s2, err := r2.ReadString('\n')
   if err != nil {
    fmt.Println(err)
   }
   b.AddBlock(s2)
  case "2":
   //遍历历区块
   fmt.Println("开始遍历区块链上的数据")
   for i, block := range b.Blocks {
    fmt.Println("----------------------------")
    fmt.Printf("第 %d 个区块的信息如下:\n", i)
    fmt.Printf("PreHash: %x\n", block.PrevBlockHash)
    fmt.Printf("Data: %s\n", string(block.Data))
    fmt.Printf("Hash: %x\n", block.Hash)
    fmt.Println("----------------------------")
   }

  case "q":
   //退出程序
   fmt.Println("已经退出")
   return
  }
 }
}

//绑定区块的hash方法
func (b *Block) SetHash() {
 //将本区块的TimeStamp，Data, PrevBlockHash拼接在一起，统一进行hash
 timestamp := []byte(strconv.FormatInt(b.Timestamp, 10))

 //将属性进行拼接
 headers := bytes.Join([][]byte{b.PrevBlockHash, b.Data, timestamp}, []byte{})

 //进行sha256hash
 hash := sha256.Sum256(headers)
 b.Hash = hash[:]
}

//创建一个区块
/*
 参数1：当前区块所保存的数据
 参数2：上一个区块的hash
*/
func NewBlock(data string, prevBlockHash []byte) *Block {
 //1.实例化一个区块对象
 block := Block{}

 //2、初始化当前区块
 block.Timestamp = time.Now().Unix()
 block.Data = []byte(data)
 block.PrevBlockHash = prevBlockHash
 block.Hash = []byte{} //还没计算，暂时赋空

 //3.给当前区块进行hash运算
 block.SetHash()
 return &block
}

func NewGenesisBlock() *Block {
 return NewBlock("Genesis Block", []byte{})
}

func NewBlockChain() *BlockChain {
 return &BlockChain{[]*Block{NewGenesisBlock()}}
}

//添加一个区块到区块链中
func (b *BlockChain) AddBlock(data string) {
 //得到前一个区块
 prevBlock := b.Blocks[len(b.Blocks)-1]
 //创建一个新区块
 newBlock := NewBlock(data, prevBlock.Hash)
 //将新区块添加到区块链中
 b.Blocks = append(b.Blocks, newBlock)
}

func main() {
 BlockChainStart()
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
