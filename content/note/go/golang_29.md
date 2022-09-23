---
author: "li_mingxie"
title: "【golang笔记】go_sync.WaitGroup(29)"
date: 2022-09-30T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`sync.WaitGroup`** 相关的内容。<!--more-->

## 1.协程的等待

我们先看看如下代码：  

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 for i := 0; i < 100; i++ {
  go fmt.Println(i)
 }
 time.Sleep(time.Second)
}
```

主线程为了等待goroutine都运行完毕，  
不得不在程序的末尾使用time.Sleep() 来睡眠一段时间  
为了解决如上问题，我们可以引进管道  

## 2.Channel的阻塞

```go
package main

import (
 "fmt"
)

func main() {
 c := make(chan bool, 100)
 for i := 0; i < 100; i++ {
  go func(i int) {
   fmt.Println(i)
   c <- true
  }(i)
 }

 for i := 0; i < 100; i++ {
  <-c
 }
}
```

这么使用，管道是能达到我们的目的的。  
但是管道在这里显得有些大材小用，在这里使用管道实际上是不合适的。  
而且假设我们有一万、十万甚至更多的for循环，也要申请同样数量大小的管道出来，对内存也是不小的开销。
对于这种情况，go语言中有一个其他的工具`sync.WaitGroup`能更加方便的帮助我们达到这个目的。  

## 3.sync.WaitGroup 实现

WaitGroup 对象内部有一个计数器，最初从0开始，  
它有三个方法：Add(), Done(), Wait() 用来控制计数器的数量。  

* Add(n) 把计数器设置为n
* Done() 每次把计数器-1
* wait() 会阻塞代码的运行，直到计数器地值减为0。

```go
package main

import (
 "fmt"
 "sync"
)

func main() {
 wg := sync.WaitGroup{}
 wg.Add(100)
 for i := 0; i < 100; i++ {
  go func(i int) {
   fmt.Println(i)
   wg.Done()
  }(i)
 }
 wg.Wait()
}
```

这里首先把wg计数设置为100，每个for循环运行完毕都把计数器减一，  
主函数中使用Wait() 一直阻塞，直到wg为零——也就是所有的100个for循环都运行完毕。  
相对于使用管道来说，WaitGroup 轻巧了许多。  

## 4.sync.WaitGroup的注意事项

#### 4.1 计数器不能为负值

我们不能使用Add() 给wg 设置一个负值，否则代码将会报错

```bash
panic: sync: negative WaitGroup counter
```

#### 4.2 WaitGroup对象不是一个引用类型

WaitGroup对象不是一个引用类型，在通过函数传值的时候需要使用地址

```go
package main

import (
 "fmt"
 "sync"
)

func main() {
 wg := sync.WaitGroup{}
 wg.Add(100)
 for i := 0; i < 100; i++ {
  go f(i, &wg)
 }
 wg.Wait()
}

// 一定要通过指针传值，不然进程会进入死锁状态
func f(i int, wg *sync.WaitGroup) {
 fmt.Println(i)
 wg.Done()
}
```

#### 4.3 WaitGroup的add操作在协程里做有可能整个协程都会丢失

最好是在协程外记录add

```go
package main

import (
 "fmt"
 "sync"
)

func main() {
 wg := sync.WaitGroup{}
 countChan := make(chan int, 100)
 for i := 0; i < 100; i++ {
  go func(countChan chan int) {
   wg.Add(1)
   countChan <- 1
   wg.Done()
  }(countChan)
 }
 wg.Wait()

 fmt.Println(len(countChan))
}
```

改成 =>

```go
package main

import (
 "fmt"
 "sync"
)

func main() {
 wg := sync.WaitGroup{}
 countChan := make(chan int, 100)
 wg.Add(100)
 for i := 0; i < 100; i++ {
  go func(countChan chan int) {
   countChan <- 1
   wg.Done()
  }(countChan)
 }
 wg.Wait()

 fmt.Println(len(countChan))
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
