---
author: "li_mingxie"
title: "【golang笔记】go_二维数组(10)"
date: 2022-09-11T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang二维数组相关的内容。<!--more-->

## 1.二维数组的内存结构

下图简单的解释了一下二维数组的内存结构。

[图片备用地址](https://limingxie.github.io/images/go/slice/array_01.png)  
![slice_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/slice/array_01.png)

#### 代码用例

```go
package main

import "fmt"

func main() {
 var arr [2][3]int

 fmt.Printf("arr[0]的地址=%p\n", &arr[0])       //arr[0]的地址=0xc0000aa030
 fmt.Printf("arr[0][0]的地址=%p\n", &arr[0][0]) //arr[0][0]的地址=0xc0000aa030

 fmt.Printf("arr[1]的地址=%p\n", &arr[1])       //arr[1]的地址=0xc0000aa048
 fmt.Printf("arr[1][0]的地址=%p\n", &arr[1][0]) //arr[1][0]的地址=0xc0000aa048
 /*
  arr[0] 和 arr[1]的 相差正好是  [3]int 的 3个 字节 24位。 16进制 24位 = 18
 */
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
