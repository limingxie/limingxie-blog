---
author: "li_mingxie"
title: "【golang笔记】go_反射(30)"
date: 2022-10-01T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`反射`** 相关的内容。<!--more-->

## 1.Go反射介绍

1. 反射可以在运行时动态获取变量的各种信息，比如变量的类型(type)类别(kind)
2. 如果是结构体变量，还可以获取到结构体本身的信息(包括字段，方法)
3. 通过反射，可以修改变量的值，调用关联方法
4. Go 是需要 import "reflect"

## 2.常用的几个概念

#### type Type

Type类型用来表示一个go类型。

不是所有go类型的Type值都能使用所有方法。  
在调用有分类限定的方法时，应先使用Kind方法获知类型的分类。  
调用该分类不支持的方法会导致运行时的panic。

```
//TypeOf返回接口中保存的值的类型，TypeOf(nil)会返回nil。
func TypeOf(i interface{}) Type
```

#### type Value

```
type Value struct {
    // 内含隐藏或非导出字段
}
```

Value为go值提供了反射接口。  
Value类型的零值表示不持有某个值。  
绝大多数函数和方法都永远不返回Value零值。  
如果某个go类型值可以安全的用于多线程并发操作，它的Value表示也可以安全的用于并发。

```
//ValueOf返回一个初始化为i接口保管的具体值的Value，ValueOf(nil)返回Value零值。
func ValueOf(i interface{}) Value
```

#### (Value)Interface

```
func (v Value) Interface() (i interface{})
```

本方法返回v当前持有的值（表示为/保管在interface{}类型），等价于：

```
var i interface{} = (v's underlying value)
```

如果v是通过访问非导出结构体字段获取的，会导致panic。

## 3.简单实例

```go
package main

import (
 "fmt"
 "reflect"
)

//专门演示反射
func reflectTest01(b interface{}) {

 //1. 先获取到 reflect.Type
 rTyp := reflect.TypeOf(b)
 fmt.Println("rType=", rTyp) //rType= int

 //2. 获取到 reflect.Value
 rVal := reflect.ValueOf(b)

 n2 := 2 + rVal.Int()
 //n3 := rVal.Float() //会报错转的类型不对
 fmt.Println("n2=", n2) // n2= 102
 //fmt.Println("n3=", n3)

 fmt.Printf("rVal=%v rVal type=%T\n", rVal, rVal) // rVal=100 rVal type=reflect.Value

 //下面我们将 rVal 转成 interface{}
 iV := rVal.Interface()
 //将 interface{} 通过断言转成需要的类型
 num2 := iV.(int)
 fmt.Println("num2=", num2) //num2= 100
}

//专门演示反射[对结构体的反射]
func reflectTest02(b interface{}) {

 //1. 先获取到 reflect.Type
 rTyp := reflect.TypeOf(b)
 fmt.Println("rType=", rTyp) //rType= main.Student

 //2. 获取到 reflect.Value
 rVal := reflect.ValueOf(b)

 //3. 获取 变量对应的Kind
 //(1) rVal.Kind() ==>
 kind1 := rVal.Kind()
 //(2) rTyp.Kind() ==>
 kind2 := rTyp.Kind()
 fmt.Printf("kind1 =%v kind2=%v\n", kind1, kind2) //kind1 =struct kind2=struct

 //下面我们将 rVal 转成 interface{}
 iV := rVal.Interface()
 fmt.Printf("iv=%v iv type=%T \n", iV, iV) //iv={tom 20} iv type=main.Student 
 //可以获取类型后，使用switch 判断
 stu, ok := iV.(Student)
 if ok {
  fmt.Printf("stu.Name=%v\n", stu.Name) //stu.Name=tom
 }
}

type Student struct {
 Name string
 Age  int
}

func main() {
 //1. int的反射例子
 var num int = 100
 reflectTest01(num)

 //2. 结构提的反射例子
 stu := Student{
  Name: "tom",
  Age:  20,
 }
 reflectTest02(stu)
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
