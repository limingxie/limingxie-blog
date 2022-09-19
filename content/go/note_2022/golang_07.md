---
author: "li_mingxie"
title: "【golang笔记】go_字符串函数(7)"
date: 2022-09-08T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang常用函数内容。<!--more-->

## 1.函数的参数传递的两种方式

* 1.值传递
* 2.引用传递

> 值类型: 基本数据类型 int系列，float系列，bool，string，数组，struct
> 引用类型: 指针，slice, map, channel, interface

## 2.字符串的常用函数

#### len(str) len(Array)

查看字符串或数组的长度

#### int,err:=strconv.Atoi(string)

string => int

#### str = strconv.Itoa(int)

int => string

#### bytes = []byte("hello")

string => byte

#### str = string([]byte{97,98,99})

byte => string

#### str = strconv.FormatInt（123, 2） 2->8, 16 //转2进制，8进制，16进制

int => 2进制，8进制，16进制

#### strings.Contains("sesssfodedd", "fod") //true

查看字符串中有指定字符

#### strings.Count("cehdese", "e") //4

统计包含的字符的数量

#### strings.EqualFold("abc", "Abc") //true

不区分大小的作比较。

```go
"abc" == "Abc"  //false
```

#### strings.Index("aa_abc", "abc") //3

返回第一次出现的index值

#### strings.LastIndex("golang go", go) //7

返回最后出现的index值，没有返回-1

#### strings.Replace("go go ha", "go", "beijing", n) //n指定替换第几个，-1表示全部

#### strings.Split("go, hello, world") // []string{"go", "hello", "world"}

字符转数组

#### strings.Join([]string{"go", "hello", "world"}, ",") //go,hello,world

数组转字符

#### strings.ToLower("Go") strings.ToUpper("Go")

字符转 大小写。

#### strings.TrimSpace(" aa bb ") // "aa bb"

去掉空格

#### strings.Trim("! he!llo ! ", " !") // he!llo

去掉空格和指定字符

#### strings.TrimLeft strings.TrimRight

#### strings.HasPrefix("ftp//aa", "ftp") //true

判断前缀包含字符

#### strings.HasSuffix("aa_abc", "abc") //true

判断后缀包含字符

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
