---
author: "li_mingxie"
title: "【golang笔记】go_封装encapsulation(13)"
date: 2022-09-14T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang封装encapsulation相关的内容。<!--more-->

## 1.概念

封装(encapsulation)就是把抽象出的字段和对字段的操作封装在一起，  
数据被保护在内部，程序的其它包只有通过被授权的操作(方法),才能对字段进行操作。

## 2.实现

1. 将结构体字段(属性)的首字母小写(类似private)
2. 给结构体所在包提供一个工厂模式的函数，首字母大写。类似一个构造函数
3. 提供一个首字母大写的Set方法Get方法(类似其它语言的public)

## 3.实例

#### 3.1 简答的例子

**创建一个models包里面创建person结构体**

```go
package models
 
import (
 "fmt"
)
 
//定义一个结构体
type person struct {
 Name string
 age int
}
//编写工厂模式函数
func NewPerson(name string) *person  {
 return &person{
  Name: name,
 }
}
//编写get和set方法
func (p *person) SetAge(age int)  {
 if age > 0 && age < 200 {
  p.age = age
 }else {
  fmt.Println("输入的年龄超出范围了...")
 }
}
func (p *person) GetAge() int {
 return  p.age
}
```

```go
package main
 
import (
 "fmt"
 "testCode/test/models"
)
func main() {
 newPerson := person.NewPerson("老王")
 newPerson.SetAge(40)
 fmt.Println("年龄:",newPerson.GetAge())
}
```

#### 3.2 练习

创建一个结构体Account:  
字段有  
账号（长度 6~10）  
余额（必须 > 20）  
密码（必须是6位数）  

```go
package models
 
import (
 "fmt"
)
 
//定义一个结构体
type account struct {
 accountNo string
 pwd string
 balance float64
}
//编写一个工厂模式
func NewAccount(accountNo string, pwd string, balance float64) *account {
 if len(accountNo) < 6 || len(accountNo) > 10  {
  fmt.Println("账户的长度不对")
  return nil
 }
 if len(pwd) != 6  {
  fmt.Println("账户密码格式不正确")
  return  nil
 }
 if balance < 20 {
  fmt.Println("存入余额不足")
  return nil
 }
 a := account{
  accountNo: accountNo,
  pwd:       pwd,
  balance:   balance,
 }
 return &a
}
func (p *account) SetAccountNo(accountNo string)  {
 if len(accountNo) < 6 || len(accountNo) > 10  {
  fmt.Println("账户的长度不对")
  return
 } else {
  p.accountNo = accountNo
 }
}
func (p *account) SetPwd(pwd string)  {
 if len(pwd) != 6  {
  fmt.Println("账户密码格式不正确")
  return
 }else {
  p.pwd = pwd
 }
}
func (p *account) SetBalance(balance float64)  {
 if balance < 20 {
  fmt.Println("存入余额不足")
  return
 }else {
  p.balance = balance
 }
}
//存款
func (account *account) Deposite(money float64,pwd string)  {
 //判断密码是否正确
 if pwd != account.pwd {
  fmt.Println("请输入正确的密码")
  return
 }
 //判断输入金额是否合理
 if money <= 0 {
  fmt.Println("请输入正确的金额")
  return
 }
 account.balance += money
 fmt.Println("存款成功")
}
//取款
func (account *account) WithDraw(money float64,pwd string)  {
 //判断密码是否正确
 if pwd != account.pwd {
  fmt.Println("请输入正确的密码")
  return
 }
 //判断输入金额是否合理
 if money <= 0 || money > account.balance{
  fmt.Println("请输入正确的金额")
  return
 }
 account.balance -= money
 fmt.Println("取款成功")
}
//查询余额
func (account *account) Query(pwd string)  {
 //判断密码是否正确
 if pwd != account.pwd {
  fmt.Println("请输入正确的密码")
  return
 }
 fmt.Println("账户余额为:",account.balance)
}
```

```go
package main
 
import (
 "fmt"
 "testCode/test/models"
)
func main() {
 
 newAccount := account.NewAccount("老王", "666666", 20)
 if newAccount != nil {
  fmt.Println("创建成功",newAccount)
 } else {
  fmt.Println("创建失败")
 }
 newAccount.SetAccountNo("5555555555555555")
 fmt.Println(newAccount)
}
```

**执行结果**

```bash
$ go run main.go
创建成功 &{老王 666666 20}
账户的长度不对
&{老王 666666 20}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
