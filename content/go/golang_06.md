---
author: "li_mingxie"
title: "【golang笔记】go_函数(6)"
date: 2022-09-07T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang函数的基本内容。<!--more-->
一些基础行的概念就略过了，整理了一些，容易忽略的地方  

## 1.函数中需要注意的地方

#### 1.1 go函数不支持重载

#### 1.2 函数的形参中，值类型和数组默认都是值传递

#### 1.3 函数也是一种数据类型，可以赋给一个变量

**给变量赋值**

```go
func getSum(n1, n2 int) int {
    return n1 + n2
}

func main() {
    a := getSum
    res := a(1,2)
    fmt.Println(res)
}
```

**函数当参数**

```go
func myFun(funvar func(int, int) int, n1 int, n2 int) int {
    return funvar(n1, n2)
}

func getSum(n1, n2 int) int {
    return n1 + n2
}

func main() {
    res := myFun(getSum, 1, 2)
    fmt.Println(res)
}
```

**声明函数类型**

```go
type myFuncType func(int, int) int

func myFun(funvar myFuncType, n1 int, n2 int) int {
    return funvar(n1, n2)
}

func getSum(n1, n2 int) int {
    return n1 + n2
}

func main() {
    res := myFun(getSum, 1, 2)
    fmt.Println(res)
}
```

#### 1.4 支持可变参数

```go
func sum(args... int){
    、、args是slice,所以通过args[index]可以访问每个值。
}
```

----------------------------------------------

## 2.init函数

#### 下图简单的整理了一下init函数执行的顺序  

[图片备用地址](https://limingxie.github.io/images/go/func/init_func_01.png)  
![init_func_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/func/init_func_01.png)

#### 简单的练习  

**test1**

```go
package test1

import "fmt"

func init() {
 fmt.Println("test1.....")
}

func Test1func() {
 fmt.Println("test1func")
}
```

**test2**

```go
package test2

import "fmt"

func init() {
 fmt.Println("test2.....")
}

func Test2func() string {
 fmt.Println("test2func")
 return "test2func"
}
```

**main**

```go
package main

import (
 "fmt"
 _ "testCode/test/test1"
 "testCode/test/test2"
)

var aa = test2.Test2func()

func main() {
 fmt.Println("main()... aa = ", aa)
}

func init() {
 fmt.Println("main init....")
}
```

**执行结果**

```bash
$ go run main.go
test1.....
test2.....
test2func
main init....
main()... aa =  test2func
```

----------------------------------------------

## 3.匿名函数

#### 3.1 在定义匿名函数时直接调用，这种方式只能调用一次

```go
package main

import "fmt"

func main() {
 res1 := func(n1 int, n2 int) int {
  return n1 + n2
 }(10, 20)
 fmt.Println(res1)
}
```

#### 3.2 将匿名函数赋给一个变量，在通过变量调用匿名函数

```go
package main

import "fmt"

func main() {
 a:= func(n1 int, n2 int) int {
  return n1 + n2
 }
 res1 := a(10, 20)
 fmt.Println(res1)
 res2 := a(20, 30)
 fmt.Println(res2)
}
```

----------------------------------------------

## 4.闭包

闭包是一个函数和与其相关的应用环境组合的一个整体。
下面看两个闭包的例子

```go
package main

import (
 "fmt"
)

func AddUpper() func(int) int {
 var n int = 0
 var str string = "str"
 return func(x int) int {
  n = n + x
  str = str + "a"
  fmt.Println("str =", str) // str = stra  str = straa  str = straaa
  return n
 }
}

func main() {
 f := AddUpper()
 fmt.Println(f(1)) // 1
 fmt.Println(f(1)) // 2
 fmt.Println(f(3)) // 5
}
```

```go
package main

import (
 "fmt"
 "strings"
)

func makeSuffix(suffix string) func(string) string {
 return func(name string) string {
  if !strings.HasSuffix(name, suffix) {
   return name + suffix
  }
  return name
 }
}

func main() {
 f := makeSuffix(".jpg")
 fmt.Println("文件名处理 =", f("aa"))     //文件名处理 = aa.jpg
 fmt.Println("文件名处理 =", f("bb.mp3")) //bb.mp3.jpg
 fmt.Println("文件名处理 =", f("bb.jpg")) //bb.jpg
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
