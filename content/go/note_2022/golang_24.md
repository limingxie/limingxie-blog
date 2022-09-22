---
author: "li_mingxie"
title: "【golang笔记】go_channel遍历和关闭(24)"
date: 2022-09-25T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`channel遍历和关闭`** 相关的内容。<!--more-->

## 1.channel的关闭

使用内置函数close可以关闭channel, channel关闭后不能向channel写入数据，但是可以从channel读取数据。

```go
package main

import (
 "fmt"
)

func main() {

 intChan := make(chan int, 3)
 intChan <- 100
 intChan <- 200
 close(intChan) // close
 //这是不能够再写入数到channel
 //intChan<- 300
 fmt.Println("大家好~")
 //当管道关闭后，读取数据是可以的
 n1 := <-intChan
 fmt.Println("n1=", n1) //n1= 100
 fmt.Println(<-intChan) //200
}
```

## 2.channle的遍历

* 遍历管道不能使用普通的 for 循环
* 在遍历时，如果channel没有关闭，则会出现deadlock的错误, channel已经关闭，则会正常遍历数据，遍历完后，就会退出遍历

```go
package main

import (
 "fmt"
)

func main() {
 //遍历管道
 intChan := make(chan int, 100)
 for i := 0; i < 100; i++ {
  intChan <- i * 2 //放入100个数据到管道
 }

 //遍历管道不要使用普通的 for 循环
 // close(intChan)
 // for i := 0; i < 100; i++ {
 //  fmt.Println(<-intChan)
 // }

// for i := 0; i < len(intChan); i++ {
 //  fmt.Println(<-intChan)
 // }
 //在遍历时，如果channel没有关闭，则会出现deadlock的错误
 //在遍历时，如果channel已经关闭，则会正常遍历数据，遍历完后，就会退出遍历
 close(intChan)
 for v := range intChan {
  fmt.Println("v=", v)
 }
}
```

## 3.应用练习

writeData写入50个数据  
readData数据，读完后程序结束  

```go
package main

import (
 "fmt"
)

//write Data
func writeData(intChan chan int) {
 for i := 1; i <= 50; i++ {
  //放入数据
  intChan <- i //
  fmt.Println("writeData ", i)
 }
 close(intChan) //关闭
}

//read data
func readData(intChan chan int, exitChan chan bool) {
 for {
  v, ok := <-intChan
  if !ok {
   break
  }
  fmt.Printf("readData 读到数据=%v\n", v)
 }
 //readData 读取完数据后，即任务完成
 exitChan <- true
 close(exitChan)
}

func main() {

 //创建两个管道
 intChan := make(chan int, 10)
 exitChan := make(chan bool, 1)

 go writeData(intChan)
 go readData(intChan, exitChan)

 //这么写也可以，其实就是等待有一条数据进来就结束。
 //<-exitChan

 for {
  _, ok := <-exitChan
  if !ok {
   break
  }
 }
}
```

## 4.channel的阻塞

如果向管道只有写，没有读取操作，会发生阻塞而产生deadlock！  
只有有写入和读取的操作，写入和读取的速率不一致也没关系。  

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
