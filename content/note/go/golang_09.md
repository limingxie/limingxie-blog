---
author: "li_mingxie"
title: "【golang笔记】go_数组和切片slice(9)"
date: 2022-09-10T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`数组和切片slice`** 相关的内容。<!--more-->

#### 在Go中数组是`【值类型】`

## 1.数组的内存结构

1. 数组的地址可以通过数组名获得。
2. 数组的第一个元素的地址就是数组的地址。
3. 第二个元素地址是，第一个元素地址 + 数组类型占用的字节数。
4. 数组之间各元素之间的间隔是依据数组的类型(大小)决定。

看下面的例子

```go
package main

import "fmt"

func main() {
 var intArr [4]int = [4]int{1, 2, 43, 34}
 fmt.Printf("intArr地址=%p | intArr[0]地址=%p | intArr[1]地址=%p | intArr[1]地址=%p \n", &intArr, &intArr[0], &intArr[1], &intArr[2])

 var intArr32 [4]int32 = [4]int32{1, 2, 43, 34}
 fmt.Printf("intArr32地址=%p | intArr32[0]地址=%p | intArr32[1]地址=%p | intArr32[2]地址=%p \n", &intArr32, &intArr32[0], &intArr32[1], &intArr32[2])
}
```

**执行结果**  

```go
$ go run main.go
 fmt.Printf("intArr地址=%p | intArr[0]地址=%p | intArr[1]地址=%p | intArr[1]地址=%p", &intArr, &intArr[0], &intArr[1], &intArr[2])  // 0和1 差 8 => int占用的字节数
 intArr32地址=0xc00018c020 | intArr32[0]地址=0xc00018c020 | intArr32[1]地址=0xc00018c024 | intArr32[2]地址=0xc00018c028 //相差 4
```

## 2.数组声明方式

```go
package main

import "fmt"

func main() {
 var intArr1 [3]int = [3]int{1, 2, 3}
 fmt.Println(intArr1)

 var intArr2 = [3]int{1, 2, 3}
 fmt.Println(intArr2)

 var intArr3 = [...]int{1, 2, 3}
 fmt.Println(intArr3)

 var intArr4 = [...]int{2: 1, 0: 2, 1: 3}
 fmt.Println(intArr4)

 var strArr = [...]string{2: "haha", 0: "heihei", 1: "hehe"}
 fmt.Println(strArr)
}
```

----------------------------------------------

## 3.切片slice

**切片(slice)是数组的一个引用**

切片的结构可以参考我之前写的 [【go笔记】切片(slice)结构的简介](https://limingxie.github.io/go/go_slice_code/)

#### append和copy

了解append和copy的底层逻辑，以下现象就很容易解释了

**append**  

```go
package main

import "fmt"

func main() {
 var intArr [6]int = [6]int{1, 2, 3, 4, 5, 6}
 slice1 := intArr[1:3]

 slice1 = append(slice1, 10)

 fmt.Println(intArr) //[1 2 3 10 5 6]
 fmt.Println(slice1) //[2 3 10]
}
```

**copy**  

```go
package main

import "fmt"

func main() {
 var intArr [6]int = [6]int{1, 2, 3, 4, 5, 6}
 slice1 := intArr[1:3]
 slice2 := intArr[2:4]

 copy(slice2, slice1)

 fmt.Println(intArr) //[1 2 2 3 5 6]
 fmt.Println(slice1) //[2 2]
 fmt.Println(slice2) //[2 3]
}

```

## 4.string切片

string是值类型不能直接修改单个字符值。  
比如：  

```go
package main

import "fmt"

func main() {
 var str string = "hello"
 str[0] = 'z' //编译错误
}
```

可以转成[]byte修改数据再转str  

```go
package main

import "fmt"

func main() {
 var str string = "hello"
 arr := []byte(str)
 arr[0] = 'z'
 str = string(arr)
 fmt.Println(str) //zello
}
```

因为byte是只能支持数字和字母，想用中文得转[]rune

```go
package main

import "fmt"

func main() {
 var str string = "hello"
 arr := []rune(str)
 arr[0] = '哈'
 str = string(arr)
 fmt.Println(str) //哈ello
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
