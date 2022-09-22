---
author: "li_mingxie"
title: "【golang笔记】go_继承和接口相互关系(16)"
date: 2022-09-17T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇文章简答的介绍了 **`继承和接口之间的关系`** ，以及特点。<!--more-->  

## 继承的作用是复用代码  

## 接口的作用是代码规范

看下面的例子，猴子可以爬树，所以小猴子需要继承猴子，继承了爬树的继承。  
(复用代码，以后猴子都可以爬树)
但是飞翔和，游泳是使用接口方式实现的。  
(代码规范，以后飞翔和游泳是以这种方式实现。)

```go
package main
import (
 "fmt"
)

//Monkey结构体
type Monkey struct {
 Name string
}

//声明接口
type BirdAble interface {
 Flying()
}

type FishAble interface {
 Swimming()
}

func (this *Monkey) climbing() {
 fmt.Println(this.Name, " 生来会爬树..")
}

//LittleMonkey结构体
type LittleMonkey struct {
 Monkey //继承
}


//让LittleMonkey实现BirdAble
func (this *LittleMonkey) Flying() {
 fmt.Println(this.Name, " 通过学习，会飞翔...")
}

//让LittleMonkey实现FishAble
func (this *LittleMonkey) Swimming() {
 fmt.Println(this.Name, " 通过学习，会游泳..")
}

func main() {

 //创建一个LittleMonkey 实例
 monkey := LittleMonkey{
  Monkey {
   Name : "悟空",
  },
 }
 monkey.climbing()
 monkey.Flying()
 monkey.Swimming()

}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
