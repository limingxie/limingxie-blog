---
author: "li_mingxie"
title: "【golang笔记】go_映射map(11)"
date: 2022-09-12T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang映射map相关的内容。<!--more-->

## 1.Map的结构

map的结构说明可以参考以前写的这篇文章。[【go笔记】map结构的简介](https://limingxie.github.io/go/go_map/)

## 2.实际操作例子

```go
package main

import "fmt"

func main() {
 //声明
 var mapAge map[string]int
 mapAge = make(map[string]int, 2)
 mapAge["ZhangSan"] = 19
 mapAge["LiSi"] = 20
 mapAge["XiaoHong"] = 21

 fmt.Println(mapAge) //map[LiSi:20 XiaoHong:21 ZhangSan:19]

 //修改
 mapAge["ZhangSan"] = 30
 fmt.Println(mapAge) //map[LiSi:20 XiaoHong:21 ZhangSan:30]

 //删除
 delete(mapAge, "ZhangSan")
 fmt.Println(mapAge) //map[LiSi:20 XiaoHong:21]

 mAge := make(map[string]int)
 mAge["XiaoMing"] = 20
 mAge["LaoZhang"] = 30
 fmt.Println(mAge) //map[LaoZhang:30 XiaoMing:20]
}

```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
