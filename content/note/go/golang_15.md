---
author: "li_mingxie"
title: "【golang笔记】go_接口interface(15)"
date: 2022-09-16T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`接口interface`** 相关的内容。<!--more-->

## 1.接口的基本结构

下面的例子可以简单的说明接口的基本结构

```go
package main
 
import "fmt"
 
type Usb interface {
 Start()
 Stop()
}
type Phone struct {
 
}
type Camera struct {
 
}
type  Computer struct {
 
}
func (p Phone) Start()  {
 fmt.Println("链接手机...")
}
func (p Phone) Stop()  {
 fmt.Println("断开手机...")
}
func (c Camera) Start()  {
 fmt.Println("链接相机...")
}
func (c Camera) Stop()  {
 fmt.Println("断开相机...")
}
func (com Computer) Working(usb Usb)  {
 usb.Start()
 usb.Stop()
}
func main() {
 phone := Phone{}
 camera := Camera{}
 computer := Computer{}
 computer.Working(phone)
 computer.Working(camera)
}
```

**执行结果**

```bash
$ go run main.go
链接手机...
断开手机...
链接相机...
断开相机...
```

## 2.细节说明

1、接口本身不能创建实例，但是可以指向一个实现了该接口的自定义类型的变量(实例)

```go
package main

import (
 "fmt"
)

type E interface {
 Say()
}
type F struct {
 Name string
}

func (b F) Say() {
 fmt.Println("say()")
}

func main() {
 var b F
 var a E = b
 a.Say()
}
```

2、接口中所有的方法都没有方法体.即都是没有实现的方法。
3、在Golang中,一个自定义类型需要将某个接口的所有方法都实现，我们说这个自定义类型实现了该接口。
4、一个自定义类型只能是实现了接口，才能将该类型的实例赋给接口。
5、只要是自定义数据类型，就可以实现接口，不仅仅是结构体类型

```go
package main

import "fmt"

type A interface {
 Say()
}
type B int

func (b B) Say() {
 fmt.Println("b=", b)
}
func main() {
 var i B = 10
 var a A = i
 a.Say()        //b= 10
 fmt.Println(a) //10
}
```

6、一个自定义类型可以实现多个接口

```go
package main

import "fmt"

type A interface {
 Say()
}
type B interface {
 Hello()
}
type C struct {
}

func (c C) Say() {
 fmt.Println("A接口被实现")
}
func (c C) Hello() {
 fmt.Println("B接口被实现")
}
func main() {
 var c C
 var a A = c
 var b B = c
 a.Say()    //A接口被实现
 b.Hello()  //B接口被实现
}
```

7、接口中不能出现任何变量
8、-一个接口(比如A接口)可以继承多个别的接口(比如B,C接口)，这时如果要实现A接口，也必须将B,C接口的方法也全部实现。

```go
package main

import "fmt"

type A interface {
 Say()
}
type B interface {
 Hello()
}
type D interface {
 A
 B
 Hei()
}
type C struct {
}

func (c C) Say() {
 fmt.Println("A接口被实现")
}
func (c C) Hello() {
 fmt.Println("B接口被实现")
}
func (c C) Hei() {
 fmt.Println("D接口被实现")
}

func main() {
 var c C
 var d D = c
 d.Hello()
 d.Say()
 d.Hei()
}
```

9、interface类型默认是一个指针(引用类型)，如果没有对interface初始化就使用那么会输出nil
10、空接口interface{}没有任何方法，所以所有类型都实现了空接口

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
