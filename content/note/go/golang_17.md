---
author: "li_mingxie"
title: "【golang笔记】go_多态polymorphism(17)"
date: 2022-09-18T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang多态polymorphism相关的内容。<!--more-->

## 1.golang中多态的2种形式

#### 1.1 多态参数

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

//多态参数
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

#### 1.2 多态数组

```go
package main
import (
 "fmt"
)

//声明/定义一个接口
type Usb interface {
 //声明了两个没有实现的方法
 Start()
 Stop()
}

type Phone struct {
 name string
}  

//让Phone 实现 Usb接口的方法
func (p Phone) Start() {
 fmt.Println("手机开始工作。。。")
}
func (p Phone) Stop() {
 fmt.Println("手机停止工作。。。")
}

type Camera struct {
 name string
}
//让Camera 实现   Usb接口的方法
func (c Camera) Start() {
 fmt.Println("相机开始工作。。。")
}
func (c Camera) Stop() {
 fmt.Println("相机停止工作。。。")
}

func main() {
 //定义一个Usb接口数组，可以存放Phone和Camera的结构体变量
 //这里就体现出多态数组
 var usbArr [3]Usb
 usbArr[0] = Phone{"vivo"}
 usbArr[1] = Phone{"小米"}
 usbArr[2] = Camera{"尼康"}
 
 fmt.Println(usbArr)

}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
