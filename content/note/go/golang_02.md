---
author: "li_mingxie"
title: "【golang笔记】go_变量的基本类型(2)"
date: 2022-09-03T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang的 **`变量类型`** 相关的内容。<!--more-->

```
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 的别名

rune // int32 的别名
    // 表示一个 Unicode 码点

float32 float64

complex64 complex128
```

## 1.整数类型

|类型|+-符号|占用空间|表示范围|
|---|---|---|---|
|int8|有|1字节|-128 ~ 127|
|int16|有|2字节|-2^15 ~ 2^15-1|
|int32|有|4字节|-2^31 ~ 2^31-1|
|int64|有|8字节|-2^63 ~ 2^63-1|

|类型|+-符号|占用空间|表示范围|
|---|---|---|---|
|uint8|无|1字节|0 ~ 255|
|uint16|无|2字节|0 ~ 2^16-1|
|uint32|无|4字节|0 ~ 2^32-1|
|uint64|无|8字节|0 ~ 2^64-1|

|类型|+-符号|占用空间|表示范围|备注|
|---|---|---|---|---|
|int|有|32位系统4个字节 64位系统8个字节|-2^31 ~ 2^31-1  -2^63 ~ 2^63-1||
|unit|无|32位系统4个字节 64位系统8个字节|0 ~ 2^32-1 0 ~ 2^64-1||
|rune|有|与int32一样|-2^31 ~ 2^31-1|表示一个Unicode码|
|byte|无|与uint8等价|0 ~ 255|当要存储字符时选用byte|

```go
var num = 100
fmt.Printf("type:%T | size: %d", num, unsafe.Sizeof(mun))
//int 8
```

## 2.浮点类型

|类型|占用空间|表示范围|
|---|---|---|
|float32|4字节|-3.403E38 ~ 3.403E38|
|float64|8字节|-1.798E308 ~ 1.798E308|

```go
num := 5.1234e2 // 5.1234 * 10^2
num1 := 5.1234E2 // 5.1234 * 10^2
mun2 := 5.1234E2 // 5.1234 / 10^2
```

## 3.字符型

golang没有专门的字符类型，如果需要存储单个字符，一般使用byte。  
字符串就是一串固定长度的字符链接起来的字符序列。  
Go的字符串是有字节组成的。

```golang
var byte = 'a'

var b int = '北'
fmt.Printf("b=%c | 对应的码值=%d",b ,b)

var c = 10 + 'a'
fmt.Pringln(c) //107
```

```
存储：字符 ==》 对应的码值 ==》 二进制 ==》 存储
读取：二进制 ==》 码值 ==》 字符 ==》 读取
```

## 4.bool

bool类型只允许保存true和false，其占有空间是1字节。  
默认值是false。  

## 5.字符串string

* 字符串一但赋值就不能修改。

```go
var str = "hello"
str[0]='a' // 这会报错。
```

* 双引号("")识别转义符，反引号(``)输出源码内容

## 6.默认值

|数据类型|默认值
|---|---|
|整型|0|
|浮点型|0|
|字符串|""|
|bool|false|

## 7.数据类型转换

#### 7.1 整形之间转换

* 没有隐式转换，都是强转。

```go
var n1 int32 = 10
var n2 int64 = int64(n1)
var n3 float32 = float32(n1)
var n3 float64 = float64(n1)
```

* 范围小的类型转范围大类型，编译不报错，但是结果会溢出

```go
var n1 int64 = 999999
var n2 int8 = int8(n1) // n2 = 63

//编译报错的情况
var d1 int32 = 12
var d2 int64
var d3 int8

d2 = d1 + 20 //编译报错
d3 = d1 + 20 //编译报错
```

* 明显溢出是编译报错，隐式溢出不报错

```go
var n1 int32 =12
var n2 int8
var n3 int8
n2 = int8(n1) + 127 // 编译通过，但是会溢出  n2 = -117
n3 = int8(n1) + 128 // 因为int8的范围小于128 直接编译报错。
```

#### 7.2 string的转换

**fmt.Sprintf**

```go
//int => string
str = fmt.Sprintf("%d", int)
//float64 => string
str = fmt.Sprintf("%f", float64)
//bool => string
str = fmt.Sprintf("%t", bool)
//char => string
str = fmt.Sprintf("%c", char)
```

**strconv**

```go
//int => string
str = strconv.Itoa(int)
//int64 => string
str = strconv.FormatInt(int64, 10)
//float64 => string
str = strconv.FormatFloat(float64, 'f', 10, 64)
//bool => string
str = strconv.FormatBool(bool)
```

```go
//string => int
int,err:=strconv.Atoi(string)
//string => int64
int64, err := strconv.ParseInt(string, 10, 64)
//string => int64
int64, err := strconv.ParseFloat(string, 64)
//string => bool
bool, err := strconv.ParseBool(string)
```

### 7.3 string转换时的注意点

> 如果转换失败，会赋该类型的默认值。

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
