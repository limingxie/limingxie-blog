---
author: "li_mingxie"
title: "【golang笔记】go_channel只读和只写(25)"
date: 2022-09-26T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`channel只读和只写`** 相关的内容。<!--more-->

## 1.channel的只读和只写声明方式

```go
package main
import (
 "fmt"
)

func main() {
 //管道可以声明为只读或者只写

 //1. 在默认情况下下，管道是双向
 //var chan1 chan int //可读可写
 
 //2 声明为只写
 var chan2 chan<- int
 chan2 = make(chan int, 3)
 chan2<- 20
 //num := <-chan2 //error

 fmt.Println("chan2=", chan2)

 //3. 声明为只读
 var chan3 <-chan int
 num2 := <-chan3
 //chan3<- 30 //err
 fmt.Println("num2", num2)
}
```

## 2.实际用处

```go
package main

import (
 "fmt"
)

//ch chan<- int, 这样ch就只能做写操作
func send(ch chan<- int, exitChan chan struct{}) {
 for i := 0; i < 10; i++ {
  ch <- i
 }
 close(ch)
 var a struct{}
 exitChan <- a
}

// ch <-chan int, 这样ch就只能做读操作
func recv(ch <-chan int, exitChan chan struct{}) {
 for {
  v, ok := <-ch
  if !ok {
   break
  }
  fmt.Println(v)
 }
 var a struct{}
 exitChan <- a
}

func main() {
 var ch chan int
 ch = make(chan int, 10)
 exitChan := make(chan struct{}, 2)
 go send(ch, exitChan)
 go recv(ch, exitChan)

 for i := 0; i < 2; i++ {
  <-exitChan
 }
 fmt.Println("结束")
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
