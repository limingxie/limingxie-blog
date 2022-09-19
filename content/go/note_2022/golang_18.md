---
author: "li_mingxie"
title: "【golang笔记】go_类型断言(18)"
date: 2022-09-19T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang**`类型断言`**相关的内容。<!--more-->

## 1.类型断言基本用法

类型断言是由于一个接口是一般类型，不知道是具体类型的时候，就需要使用类型断言。

```go
package main

import (
 "fmt"
)

func main() {
 //类型断言(带检测的)
 var a interface{}
 var b float32 = 2.1
 a = b //空接口，可以接收任意类型

 //类型断言(带检测的)
 if c, ok := a.(float32); ok {
  fmt.Println("convert success")
  fmt.Printf("c 的类型是 %T 值是=%v\n", c, c)
 } else {
  fmt.Println("convert fail")
 }
 fmt.Println("继续执行...")
}
```

## 2.类型断言 type 用法

可以接结合 switch 语句，在条件中使用.(type)获取数据类型。

```go
package main
import (
 "fmt"
)

//定义Student类型
type Student struct {

}

//编写一个函数，可以判断输入的参数是什么类型
func TypeJudge(items... interface{}) {
 for index, x := range items {
  switch x.(type) {
   case bool :
    fmt.Printf("第%v个参数是 bool 类型，值是%v\n", index, x)
   case float32 :
    fmt.Printf("第%v个参数是 float32 类型，值是%v\n", index, x)
   case float64 :
    fmt.Printf("第%v个参数是 float64 类型，值是%v\n", index, x)
   case int, int32, int64 :
    fmt.Printf("第%v个参数是 整数 类型，值是%v\n", index, x)
   case string :
    fmt.Printf("第%v个参数是 string 类型，值是%v\n", index, x)
   case Student :
    fmt.Printf("第%v个参数是 Student 类型，值是%v\n", index, x)
   case *Student :
    fmt.Printf("第%v个参数是 *Student 类型，值是%v\n", index, x)
   default :
    fmt.Printf("第%v个参数是  类型 不确定，值是%v\n", index, x)
  }
 }
}

func main() {
 var n1 float32 = 1.1
 var n2 float64 = 2.3
 var n3 int32 = 30
 var name string = "tom"
 address := "北京"
 n4 := 300

 stu1 := Student{}
 stu2 := &Student{}

 TypeJudge(n1, n2, n3, name, address, n4, stu1, stu2)
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
