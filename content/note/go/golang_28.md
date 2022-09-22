---
author: "li_mingxie"
title: "【golang笔记】go_协程的panic处理(28)"
date: 2022-09-29T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`协程的panic处理`** 相关的内容。<!--more-->

## recover

有多个协程同时运行时，其中的一个协程出panic有可能整个程序会崩溃。  
为了防止这种现象，我们可以defer + recover 的方式处理异常。  

```go
package main
import (
 "fmt"
 "time"
)

//函数
func sayHello() {
 for i := 0; i < 10; i++ {
  time.Sleep(time.Second)
  fmt.Println("hello,world")
 }
}
//函数
func test() {
 //这里我们可以使用defer + recover
 defer func() {
  //捕获test抛出的panic
  if err := recover(); err != nil {
   fmt.Println("test() 发生错误", err)
  }
 }()
 //定义了一个map
 var myMap map[int]string
 myMap[0] = "golang" //error
}

func main() {

 go sayHello()
 go test()

 for i := 0; i < 10; i++ {
  fmt.Println("main() ok=", i)
  time.Sleep(time.Second)
 }

}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
