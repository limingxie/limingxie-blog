---
author: "li_mingxie"
title: "【golang笔记】go_error处理(8)"
date: 2022-09-09T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang错误处理相关的内容。<!--more-->

## 1.错误处理

错误可以使用defer, recover, panic 做处理。

```go
package main

import (
 "fmt"
)

func test() {
 defer func() {
  err := recover()
  if err != nil {
   fmt.Println(err)
  }
 }()

 var n1 int = 10
 var n2 int = 0
 fmt.Println(n1 / n2) //这边出错，直接走了 recover 不走下面的方法了

 fmt.Println("haha....")

 fmt.Println(n1 / n2)
}

func main() {

 test()
 fmt.Println("执行到最后了....")
}
```

**执行结果**  

```bash
$ go run main.go
runtime error: integer divide by zero
执行到最后了....
```

## 2.自定义error

error类型是一个接口类型。

```go
type error interface {
    Error() string
}
```

> 所以我们只要实现了Error()方法就能自定义实现error。

下面举了一个简单的例子。  

```go
package main

import (
 "errors"
 "fmt"
)

type MyError struct {
 level   int8
 message string
 err     error
}

func (e *MyError) Error() string {
 if e.message != "" {
  return fmt.Sprintf("MyError => level: %d  mesage: %s  err: %v", e.level, e.message, e.err)
 }
 return e.err.Error()
}

func test(isErr bool) error {
 if isErr {
  return &MyError{level: 1, message: "我的自定义错误", err: errors.New("错误")}
 } else {
  return nil
 }
}

func main() {
 err := test(true)
 if err != nil {
  fmt.Println(err)
 }
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
