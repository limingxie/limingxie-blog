---
author: "li_mingxie"
title: "【golang笔记】go_反射几点注意事项(31)"
date: 2022-10-02T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`反射几点注意事项`** 相关的内容。<!--more-->

## 1.reflect.Value.Kind，获取变量的类别，返回的是一个常量

```
type Kind uint
```

Kind代表Type类型值表示的具体分类。零值表示非法分类。

```go
const (
    Invalid Kind = iota
    Bool
    Int
    Int8
    Int16
    Int32
    Int64
    Uint
    Uint8
    Uint16
    Uint32
    Uint64
    Uintptr
    Float32
    Float64
    Complex64
    Complex128
    Array
    Chan
    Func
    Interface
    Map
    Ptr
    Slice
    String
    Struct
    UnsafePointer
)
```

## 2.Kind的范围比Type大

```
var num int = 10 // num的 type是 int Kind也是 int
var stu Student // stu的 type是 pkg.Student Kind是 struct
```

## 3.按需求 变量 <-> interface{} <-> reflect.Value 之间相互转换

```go
value := reflect.ValueOf(interface{})
num := interface{}.(int)
```

## 4.使用反射的方式获取凉凉的值，需要做数据类型匹配，要不会报Panic

```go
if reflect.TypeOf(value).Kind() == reflect.Int {
  num := value.(int)
}
```

## 5.通过反射修改变量值，使用Setxx方法需要传指针类型，获取元素使用reflect.Value.Elem()

```go
package main

import (
 "fmt"
 "reflect"
)

func testInt(b interface{}) {
 val := reflect.ValueOf(b)
 fmt.Printf("val type=%T\n", val) // val type=reflect.Value

 val.Elem().SetInt(110)
 fmt.Printf("val=%v\n", val) // val=0xc0000140b8
}

func main() {
 var num int = 20
 testInt(&num)
 fmt.Println("num =", num) //num = 110
}
```

## 6.reflect.Value.Elem() 的实际效果

```go
package main

import (
 "fmt"
 "reflect"
)

func main() {
 var num int = 10
 fn := reflect.ValueOf(&num)
 fn.Elem().SetInt(20)
 fmt.Printf("%+v\n", num)

 var num1 = 100
 var b *int = &num1
 *b = 200
 fmt.Printf("%+v\n", num1)
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
