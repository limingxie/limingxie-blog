---
author: "li_mingxie"
title: "【golang笔记】go_指针(3)"
date: 2022-09-04T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang指针类型相关的内容。<!--more-->  

## 基本结构

```go
var i int = 10
var ptr *int = &i

//i ---> 10   
//prt ---> 地址 ---> 10

*ptr = 20

//i ---> 20   
//prt ---> 地址 ---> 20
```

## string的测试

```go
var str string = "aa"
var ptr *string = &str

fmt.Printf("str:%v %v\n", &str, str)            //str:0xc000010250 aa
fmt.Printf("ptr:%v %v %v\n", &ptr, ptr, *ptr)   //ptr:0xc00000e028 0xc000010250 aa

*ptr = "bb"

fmt.Printf("str:%v %v\n", &str, str)            //str:0xc000010250 aa
fmt.Printf("ptr:%v %v %v\n", &ptr, ptr, *ptr)   //ptr:0xc00000e028 0xc000010250 aa
```

## 影响指针理解的内容

值类型的指针: int系列，float系列，bool，string，数组，结构体(struct)  
引用类型: 指针，slice切片，map，管道chan，interface。  

值类型：var num int = 10  
num ---> 值10  

引用类型：  
ref ---> 地址prt ---> 值  

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
