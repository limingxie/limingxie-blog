---
author: "li_mingxie"
title: "【golang笔记】go_channel(23)"
date: 2022-09-24T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`管道channel`** 相关的内容。<!--more-->

## 1.Goroutine 和 Channel 的关系

Go语言是通过 Goroutine 和 Channel 实现并发编程。  
Goroutine 用于执行并发任务，Channel 用于 goroutine 之间的同步、通信。

在Golang的并发哲学里，有一句非常著名的话：

> Do not communicate by sharing memory; instead, share memory by communicating.
> 不要通过共享内存来通信，而要通过通信来实现内存共享。

它依赖CSP（Communication Sequence Process） 模型，简称通信顺序进程。

```
 -----------               ----------- 
| Goroutine | - Channel - | Goroutine |
 -----------               ----------- 
      |                         |    
    Channel                  Channel
      |                         |
 -----------               ----------- 
| Goroutine | - Channel - | Goroutine |
 -----------               ----------- 
```

## 2.全局变量加锁实现

```go
package main

import (
 "fmt"
 "sync"
 "time"
 _ "time"
)

// 需求：现在要计算 1-20 的各个数的阶乘，并且把各个数的阶乘放入到map中。
// 最后显示出来。要求使用goroutine完成

var (
 myMap = make(map[int]int, 10)
 //声明一个全局的互斥锁
 //lock 是一个全局的互斥锁，
 //sync 是包: synchornized 同步
 //Mutex : 是互斥
 lock sync.Mutex
)

// test 函数就是计算 n!, 让将这个结果放入到 myMap
func test(n int) {
 res := 1
 for i := 1; i <= n; i++ {
  res *= i
 }

 //这里我们将 res 放入到myMap
 //加锁
 lock.Lock()
 myMap[n] = res //concurrent map writes?
 //解锁
 lock.Unlock()
}

func main() {
 // 我们这里开启多个协程完成这个任务[200个]
 for i := 1; i <= 20; i++ {
  go test(i)
 }

 //如果不等等，结果计算不到20 有可能到10+就直接进入下一个查询方法了
 //查询方法走完，主线程结束
 time.Sleep(time.Second * 2) 

 //这里我们输出结果,变量这个结果
 lock.Lock()
 for i, v := range myMap {
  fmt.Printf("map[%d]=%d\n", i, v)
 }
 lock.Unlock()
}
```

> go build -race 可查看执行的时候，资源详情。

## 3.channel 简介

* channel的本质就是一个数据结构-队列
* 数据是先进先出(FIFO：first in first out)
* 线程安全，多goroutine访问时，不需要加锁，channel本身线程是安全的
* channel是有类型的，一个string的channel只能存放string类型的数据。

## 4.channel的简答例子

```go
package main

import (
 "fmt"
)

func main() {
 //演示一下管道的使用
 //1. 创建一个可以存放3个int类型的管道
 var intChan chan int
 intChan = make(chan int, 3)

 //2. 看看intChan是什么
 fmt.Printf("intChan 的值=%v intChan本身的地址=%p\n", intChan, &intChan)

 //3. 向管道写入数据
 intChan <- 10
 num := 20
 intChan <- num
 intChan <- 30
 //intChan <- 98 //如果超出容量会报deadlock!错

 //4. 看看管道的长度和cap(容量)
 fmt.Printf("channel len= %v cap=%v \n", len(intChan), cap(intChan)) // 3, 3

 //5. 从管道中读取数据
 var num2 int
 num2 = <-intChan
 fmt.Println("num2=", num2)
 fmt.Printf("channel len= %v cap=%v \n", len(intChan), cap(intChan)) // 2, 3

 num3 := <-intChan
 num4 := <-intChan

 //num5 := <-intChan //在没有使用协程的情况下，如果我们的管道数据已经全部取出，再取就会报告 deadlock

 fmt.Println("num3=", num3, "num4=", num4 /*, "num5=", num5*/)
}
```

## 5.channel的几个注意事项

* channel中只能存放指定的数据类型
* channel的数据放满后，就不能再放入了
* 虽然满了，但是从channel取数据后是可以继续放入
* 在没有使用协程的情况下，如果channel数据取完了，再取，就会报deadlock错误。

## 6.练习

```go
package main

import (
 "fmt"
)

type Cat struct {
 Name string
 Age  int
}

func main() {
 //定义一个存放任意数据类型的管道 3个数据
 //var allChan chan interface{}
 allChan := make(chan interface{}, 3)

 allChan <- 10
 allChan <- "tom jack"
 cat := Cat{"小花猫", 4}
 allChan <- cat

 //我们希望获得到管道中的第三个元素，则先将前2个推出
 <-allChan
 <-allChan

 newCat := <-allChan //从管道中取出的Cat是interface类型

 fmt.Printf("newCat=%T , newCat=%v\n", newCat, newCat)
 //下面的写法是错误的!编译不通过
 //fmt.Printf("newCat.Name=%v", newCat.Name)
 a := newCat.(Cat)
 fmt.Printf("newCat.Name=%v", a.Name)
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
