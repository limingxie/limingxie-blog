---
author: "li_mingxie"
title: "【golang笔记】go_goroutine(22)"
date: 2022-09-23T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`goroutine`** 相关的内容。<!--more-->

## 1.进程process和线程thread

一个任务就是一个进程（Process），  
进程内的“子任务”称为线程（Thread）。  

## 2.并行，并发

goroutine中  
多线程在多核上运行是并行。  
多线程在单核上运行是并发。

## 3.Go语言中协程和主线程的关系

Go语言中一个主线程可以启很多协程

**协程的特点：**  

* 有独立的栈空间  
* 共享程序堆空间  
* 调度由用户控制
* 协程是轻量级的线程

## 4.GMP模型

可以参考之前写的文章  
[【go笔记】goroutine调度器的GMP模型简介](https://limingxie.github.io/go/go_routine/)

## 5.goroutine的简单实例

```go
package main
import (
 "fmt"
 "strconv"
 "time"
)

func test() {
 for i := 1; i <= 10; i++ {
  fmt.Println("tesst () hello,world " + strconv.Itoa(i))
  time.Sleep(time.Second)
 }
}

func main() {

 go test() // 开启了一个协程

 for i := 1; i <= 10; i++ {
  fmt.Println(" main() hello,golang" + strconv.Itoa(i))
  time.Sleep(time.Second)
 }
}
```

```bash
$ go run main.go
 main() hello,golang1
tesst () hello,world 1
tesst () hello,world 2
 main() hello,golang2
 main() hello,golang3
tesst () hello,world 3
tesst () hello,world 4
 main() hello,golang4
 main() hello,golang5
tesst () hello,world 5
 main() hello,golang6
tesst () hello,world 6
 main() hello,golang7
tesst () hello,world 7
tesst () hello,world 8
 main() hello,golang8
tesst () hello,world 9
 main() hello,golang9
 main() hello,golang10
tesst () hello,world 10
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
