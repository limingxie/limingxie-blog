---
author: "li_mingxie"
title: "【算法笔记】稀疏数组sparseArray的简单实践"
date: 2022-10-04T23:28:49+08:00 
tags: [
    "sparse array",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

今天用go语言简单的写了一下稀疏数组的简单实例饿。  
为了以后方便查看，当做笔记整理了一下~~  
<!--more-->

## 1.稀疏数组结构

[图片备用地址](https://limingxie.github.io/images/algorithm/base/sparse_array_01.png)  
![map_reduce_10](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/sparse_array_01.png)

## 2.代码实例

```go
package main

import "fmt"

type ValNode struct {
 row int
 col int
 val int
}

func main() {

 //创建数组
 var result [11][11]int
 result[1][1] = 1
 result[2][2] = 2
 result[3][3] = 1
 result[4][4] = 2

 //遍历打印
 for _, v := range result {
  for _, v2 := range v {
   fmt.Printf("%d  ", v2)
  }
  fmt.Println()
 }

 var sparseArr []ValNode

 //标准的一个稀疏数组应该还有一个 记录元素的二维数组的规模(行和列，默认值)
 //创建一个ValNode 值结点
 valNode := ValNode{
  row: 8,
  col: 8,
  val: 0,
 }

 sparseArr = append(sparseArr, valNode)

 for i, v := range result {
  for j, v2 := range v {
   if v2 != 0 {
    //创建一个ValNode 值结点
    valNode := ValNode{
     row: i,
     col: j,
     val: v2,
    }
    sparseArr = append(sparseArr, valNode)
   }
  }

 }

 //输出稀疏数组
 fmt.Println("当前的稀疏数组是:::::")
 for i, valNode := range sparseArr {
  fmt.Printf("%d: %d %d %d\n", i, valNode.row, valNode.col, valNode.val)
 }

 var result2 [8][8]int

 // 遍历 sparseArr [遍历文件每一行]
 for i, valNode := range sparseArr {
  if i != 0 { //跳过第一行记录值
   result2[valNode.row][valNode.col] = valNode.val
  }
 }

 // 看看chessMap2 是不是恢复.
 fmt.Println("恢复后的原始数据......")
 for _, v := range result2 {
  for _, v2 := range v {
   fmt.Printf("%d  ", v2)
  }
  fmt.Println()
 }
}
```

#### 执行结果

```bash
$ go run main.go
0  0  0  0  0  0  0  0  0  0  0  
0  1  0  0  0  0  0  0  0  0  0  
0  0  2  0  0  0  0  0  0  0  0  
0  0  0  1  0  0  0  0  0  0  0  
0  0  0  0  2  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  0  0  0  
当前的稀疏数组是:::::
0: 8 8 0
1: 1 1 1
2: 2 2 2
3: 3 3 1
4: 4 4 2
恢复后的原始数据......
0  0  0  0  0  0  0  0  
0  1  0  0  0  0  0  0  
0  0  2  0  0  0  0  0  
0  0  0  1  0  0  0  0  
0  0  0  0  2  0  0  0  
0  0  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  
0  0  0  0  0  0  0  0  
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
