---
author: "li_mingxie"
title: "【golang笔记】go_channel的select(27)"
date: 2022-09-28T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`channel的select`** 相关的内容。<!--more-->

## 1.select 介绍

管道(channel)实现了多个goroutine之前的同步或者通信，  
select实现了多个管道(channel)的同步或者通信，并且select具有阻塞的特性。  

```go
package main

import (
 "fmt"
)

func main() {

 //使用select可以解决从管道取数据的阻塞问题

 //1.定义一个管道 10个数据int
 intChan := make(chan int, 10)
 for i := 0; i < 10; i++ {
  intChan <- i
 }
 //2.定义一个管道 5个数据string
 stringChan := make(chan string, 5)
 for i := 0; i < 5; i++ {
  stringChan <- "hello" + fmt.Sprintf("%d", i)
 }

 //传统的方法在遍历管道时，如果不关闭会阻塞而导致 deadlock

 //问题，在实际开发中，可能我们不好确定什么关闭该管道.
 //可以使用select 方式可以解决
 //label:
 for {
  select {
  //注意: 这里，如果intChan一直没有关闭，不会一直阻塞而deadlock
  //，会自动到下一个case匹配
  case v := <-intChan:
   fmt.Printf("从intChan读取的数据%d\n", v)
  case v := <-stringChan:
   fmt.Printf("从stringChan读取的数据%s\n", v)
  default:
   close(intChan)
   close(stringChan)
   fmt.Printf("都取不到了，不玩了, 程序员可以加入逻辑\n")
   return
   //break label
  }
 }
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
