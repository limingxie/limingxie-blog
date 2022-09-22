---
author: "li_mingxie"
title: "【golang笔记】go_继承inheritance(14)"
date: 2022-09-15T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`继承inheritance`** 相关的内容。<!--more-->

## 1.继承的基本结构

下面的例子可以简单的说明继承的基本结构

```go
package main
 
import (
 "fmt"
)
 
type Student struct {
 Name string
 Age int
 Score int
}
//小学生结构体
type Pupil struct {
 Student //匿名结构体
}
//大学生结构体
type Graduate struct {
 Student //匿名结构体
}
 
//显示学生成绩
func (stu *Student) ShowInfo()  {
 fmt.Println("学生名:",stu.Name,"学生年龄:",stu.Age,"学生成绩:",stu.Score)
}
//输入学生考试成绩
func (stu *Student) SetScore(score int)  {
  stu.Score = score
}
func (p *Pupil) Tesing()  {
 fmt.Println("小学生正在考试")
}
func (p *Graduate) Tesing()  {
 fmt.Println("大学生正在考试")
}
func main() {
 //调用小学生结构体
 pupil := Pupil{}
 pupil.Student.Name = "小王"
 pupil.Student.Age = 7
 pupil.Tesing()
 pupil.SetScore(66)
 pupil.ShowInfo()
 
 graduate := Graduate{}
 graduate.Name = "大王"
 graduate.Age = 20
 graduate.Tesing()
 graduate.SetScore(560)
 graduate.ShowInfo()
}
```

## 2.几个细节

1. 结构体可以使用嵌套匿名结构体所有的字段和方法，  
    即:首字母大写或者小写的字段、方法，都可以使用，仅在本包中

```go
package main

import "fmt"

type A struct {
 Name string
 age  int
}
type B struct {
 A
}

func (a *A) SayName() {
 fmt.Println("name=", a.Name)
}
func (a *A) sayAge() {
 fmt.Println("aget=", a.age)
}
func main() {
 b := B{}
 b.Name = "老王"
 b.age = 18
 b.SayName() //name=老王
 b.sayAge()  //age=18
}
```

2. 当结构体和匿名结构体有相同的字段或者方法时，编译器采用就近访问原则访问，  
    如希望访问匿名结构体的字段和方法，可以通过匿名结构体名来区分。

```go
package main

import "fmt"

type A struct {
 Name string
}
type B struct {
 A
 Name string
}

func (a *A) SayName() {
 fmt.Println("a.name=", a.Name)
}
func (b *B) SayName() {
 fmt.Println("b.name=", b.Name)
}
func main() {
 b := B{}
 b.Name = "老王"
 b.SayName()   //b.name= 老王
 b.A.SayName() //a.name= 
}
```

3. 结构体嵌入两个(或多个)匿名结构体，如两个匿名结构体有相同的字段和方法(同时结构体本身没有同名的字段和方法)，在访问时，就必须明确指定置名结构体名字，否则编译报错。

```go
package main

type A struct {
 Name string
 Age  int
}
type B struct {
 Name string
}
type C struct {
 A
 B
}

func main() {
 c := C{}
 //c.Name = "老王" //这么写会报错
 c.A.Name = "老王"
}
```

4. 如果一个struct嵌套了一个有名结构体，这种模式就是组合，  
    如果是组合关系，那么在访问组合的结构体的字段或方法时，必须带上结构体的名字

```go
package main

import "fmt"

type A struct {
 Name string
 Age  int
}
type B struct {
 a A //有名结构体
}

func main() {
 b := B{}
 //b.Name = "老王" 这么写会编译报错
 b.a.Name = "老王"
 fmt.Println(b)
}
```

5. 嵌套匿名结构体后，也可以在创建结构体变量(实例)时，直接指定各个匿名结构体字段的值

```go

package main
 
import "fmt"
 
type Goods struct {
 Name string
 Price float32
}
type Brand struct {
 Name string
 Address string
}
type TV struct {
 Goods
 Brand
}
type TV2 struct {
 *Goods
 *Brand
}
func main() {
 tv1 := TV{Goods{"电脑1", 4000}, Brand{"联想", "四川"},}
 fmt.Println(tv1)
 tv2 := TV{Goods{Name: "电脑2", Price: 5000}, Brand{Name: "惠普", Address:"福建"},}
 fmt.Println(tv2)
 tv3 := TV2{&Goods{"电脑1", 4000}, &Brand{"联想", "四川"},}
 fmt.Println(*tv3.Brand,*tv3.Goods)
}
```

6. 其他

```go

package main
 
import "fmt"
 
type A struct {
 Name string
 Age int
}
type B struct {
 A
 int //可以这么写一个匿名的
}
 
func main() {
 b := B{}
 b.Name = "老王"
 b.int = 10
 b.Age = 20
 fmt.Println(b) // {{老王 20} 10}
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
