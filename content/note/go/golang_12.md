---
author: "li_mingxie"
title: "【golang笔记】go_结构体struct(12)"
date: 2022-09-13T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`结构体struct`** 相关的内容。<!--more-->

## 1.结构体struct的内存结构

下面的例子可以简单的说明结构体的内存结构

```go
package main

import "fmt"

type A struct {
 x int
 y int
 z int
}

func main() {
 a := A{x: 10, y: 20, z: 30}
 fmt.Printf("a的地址=%p\n", &a)   //a的地址=0xc0000b6000  结构体的地址和第一个字段的地址是一样的
 fmt.Printf("x的地址=%p\n", &a.x) //x的地址=0xc0000b6000  结构体的个字段之间的相差是 类型的的大小
 fmt.Printf("y的地址=%p\n", &a.y) //y的地址=0xc0000b6008
 fmt.Printf("z的地址=%p\n", &a.z) //z的地址=0xc0000b6010

}
```

## 2.工厂模式

引用了一个包，但是那个包里的结构体是小写的时候，可以用工厂模式解决。

**models**

```go
package models

type student struct {
 Name string
 age  int
}

func NewStudent(name string, age int) *student {
 return &student{
  Name: name,
  age:  age,
 }
}

func (s *student) GetAge() int {
 return s.age
}
```

**main**  

```go
package main

import (
 "fmt"
 "testCode/test/models"
)

func main() {
 student := models.NewStudent("张三", 19)
 fmt.Println(student)           //&{张三 19}
 fmt.Println(student.Name)      //张三
 fmt.Println(student.GetAge())  //19
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
