---
author: "li_mingxie"
title: "【golang笔记】go_channel实用案例(26)"
date: 2022-09-27T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`channel实用案例`** 相关的内容。<!--more-->

## 1.查询1~80000的素数

#### 思路整理

先创建3个管道：  
intchan => 启用一个协程往这里添加1~80000的数据  
resChan => 启动的n个协程从 intchan读取数据后，计算出的素数往这个管道中添加。  
exitChan => 每个协程结束任务，往这个管道中添加是否结束的表示符。 当数据达到启用的协程N个，代表所有的任务都跑完。  

#### 代码

```go
package main

import (
 "fmt"
 "time"
)

//给管道写入数据
func pushIntChan(intChan chan int) {
 for i := 1; i <= 80000; i++ {
  intChan <- i
 }
 close(intChan)
}

//计算素数
func calculation(intChan, resChan chan int, exitChan chan bool) {
 for {
  //如果 ok =false 说明管道已没数据，退出循环
  num, ok := <-intChan
  if !ok {
   //退出时给exitChan管道输入一个值，表示我这个协程结束。
   exitChan <- true
   break
  }
  //判断是不是素数的标签
  var flag bool = true
  for i := 2; i < num; i++ {
   if num%i == 0 {
    flag = false
    break
   }
  }
  if flag {
   resChan <- num
  }
 }
}

func main() {
 var aa time.Time = time.Now()
 //要是用的协程数
 var n int = 4
 //创建3个管道
 intChan := make(chan int, 1000)  // 输入数据
 resChan := make(chan int, 10000) // 计算素数结果的管道
 exitChan := make(chan bool, n)   // 判断所有的协程都结束的管道

 //输入数据
 go pushIntChan(intChan)

 //启用协程计算素数
 for i := 0; i < n; i++ {
  go calculation(intChan, resChan, exitChan)
 }

 go func() {
  //等待所有的协程结束
  for i := 0; i < n; i++ {
   <-exitChan
  }
  close(resChan)
  close(exitChan)
 }()
 //关闭管道

 for {
  v, ok := <-resChan
  if !ok {
   break
  }
  fmt.Println(v)
 }

 //这么写速度更快
 //等待所有的协程结束
 // for i := 0; i < n; i++ {
 //  <-exitChan
 // }
 // //关闭管道
 // close(exitChan)
 // close(resChan)

 // fmt.Println(len(resChan))

 // for v := range resChan {
 //  fmt.Println(v)
 // }

 fmt.Println(time.Now().Sub(aa))
}

```

## 2.sync.WaitGroup的简单使用

```go
package main

import (
 "fmt"
 "sync"
)

func sender(ch chan int, wg *sync.WaitGroup) {
 for i := 0; i < 10; i++ {
  ch <- i
 }
 close(ch)
 wg.Done()
}
func receiver(ch chan int, wg *sync.WaitGroup) {
 for {
  if val, ok := <-ch; ok {
   fmt.Println(fmt.Sprintf("%d,%s", val, "revevier"))
  } else {
   fmt.Println("quit recevier")
   break
  }
 }
 wg.Done()
}
func receiver2(ch chan int, wg *sync.WaitGroup) {
 for {
  if val, ok := <-ch; ok {
   fmt.Println(fmt.Sprintf("%d,%s", val, "revevier2"))
  } else {
   fmt.Println("quit recevier2")
   break
  }
 }
 wg.Done()
}
func main() {
 ch := make(chan int, 0)
 wg := &sync.WaitGroup{}
 wg.Add(1)
 go sender(ch, wg)
 wg.Add(1)
 go receiver(ch, wg)
 wg.Add(1)
 go receiver2(ch, wg)
 wg.Wait()
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
