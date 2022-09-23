---
author: "li_mingxie"
title: "【golang】channel介绍"
date: 2022-09-22T07:28:49+08:00
tags: [
    "channel",
    "make",
    "select",
    "close",
]
categories: [
    "golang",
]
---

Go语言是通过 Goroutine 和 Channel 实现并发编程。  
Goroutine 用于执行并发任务，Channel 用于 goroutine 之间的同步、通信。<!--more-->

在Golang的并发哲学里，有一句非常著名的话：

> Do not communicate by sharing memory; instead, share memory by communicating.
> 不要通过共享内存来通信，而要通过通信来实现内存共享。

它依赖CSP（Communication Sequence Process） 模型，简称通信顺序进程。

```
 -----------               ----------- 
| Goroutine | - Channel - | Goroutine |
 -----------               ----------- 
      |                         |    
    Channel                  Channel
      |                         |
 -----------               ----------- 
| Goroutine | - Channel - | Goroutine |
 -----------               ----------- 
```

----------------------------------------------

## 1.Channel定义，赋值，取值

Channel的操作符是 **`<-`**

```go
//定义chan
var ch chan int
//分配空间
ch = make(chan interface{}, 3)
//赋值
ch <- 10
//取值
<- ch
```

#### 实例

```go
package main

import (
 "fmt"
)

func main() {
 ch := make(chan int, 2)
 ch <- 10
 ch <- 20

 num := <-ch
 fmt.Println(num)
 fmt.Println(<-ch)
}
```

```go
$ go run main.go
10
20
```

----------------------------------------------

## 2.Channel类型

Channel有 3中类型， chan <-chan(只读) chan<-(只写)

#### 例子

```go
package main
import (
 "fmt"
)

func main() {
 //管道可以声明为只读或者只写

 //1. 在默认情况下下，管道是双向
 //var chan1 chan int //可读可写
 
 //2 声明为只写
 var chan2 chan<- int
 chan2 = make(chan int, 3)
 chan2<- 20
 //num := <-chan2 //error

 fmt.Println("chan2=", chan2)

 //3. 声明为只读
 var chan3 <-chan int
 num2 := <-chan3
 //chan3<- 30 //err
 fmt.Println("num2", num2)
}
```

----------------------------------------------

## 3.Channel遍历

Channel的遍历是使用用 for range

```go
package main

import (
 "fmt"
)

func main() {
 //遍历管道
 intChan := make(chan int, 5)
 for i := 1; i <= 5; i++ {
  intChan <- i
 }

 //在遍历时，如果channel没有关闭，则会出现deadlock的错误
 //在遍历时，如果channel已经关闭，则会正常遍历数据，遍历完后，就会退出遍历
 close(intChan)
 for v := range intChan {
  fmt.Println("v=", v)
  if v == 4 {
   break
  }
 }
 fmt.Println(<-intChan)
}
```

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 //遍历管道
 intChan := make(chan int, 5)
 for i := 1; i <= 5; i++ {
  intChan <- i
 }

 //在另一个协程中遍历是不用close
 go func(intChan chan int) {
  for v := range intChan {
   fmt.Println("v=", v)
   if v == 4 {
    break
   }
  }
 }(intChan)
 time.Sleep(time.Second * 2)
 fmt.Println(<-intChan)
}
```

```go
$ go run main.go
v= 1
v= 2
v= 3
v= 4
5
```

----------------------------------------------

## 4.阻塞blocking

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 ch := make(chan int, 2)
 go func() {
  time.Sleep(time.Second * 5)
  ch <- 10
 }()

 //这里会产生阻塞，等到ch有了值取出才能继续
 num := <-ch
 fmt.Println(num)
}
```

还可以使用for循环做判断，但是这么判断很有可能消耗没必要的cpu资源。

```go
package main

import (
 "time"
)

func main() {
 ch := make(chan int, 2)
 go func() {
  time.Sleep(time.Second * 2)
  ch <- 10
  //buclose的话 下面你的 for{ _, ok:= ... } 会报错
  close(ch)
 }()

 //这里会产生阻塞，等到ch有了值取出才能继续
 for {
  //这里的ok可以判断channel是否关闭或没有数据
  _, ok := <-ch
  if !ok {
   break
  }
 }
}
```

----------------------------------------------

## 5.Buffered Channels

Channel的缓冲是make的时候可以设定，如果没有设定缓冲只能是阻塞式的运行。  

#### 没有缓冲  

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 ch := make(chan int)
 go func() {
  ch <- 10
  ch <- 20
  ch <- 30
  close(ch)
  fmt.Println("数据放入结束")
 }()

 for v := range ch {
  time.Sleep(time.Second * 1)
  fmt.Println(v)
 }
}
```

```bash
$ go run main.go
10
20
数据放入结束
30
```

#### 设置缓冲区

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 ch := make(chan int, 3)
 go func() {
  ch <- 10
  ch <- 20
  ch <- 30
  close(ch)
  fmt.Println("数据放入结束")
 }()

 for v := range ch {
  time.Sleep(time.Second * 1)
  fmt.Println(v)
 }
}
```

```bash
$ go run main.go
数据放入结束
10
20
30
```

----------------------------------------------

## 6.select

管道(channel)实现了多个goroutine之前的同步或者通信，  
select实现了多个管道(channel)的同步或者通信，并且select具有阻塞的特性。  

```go
package main

import (
 "fmt"
)

func main() {

 //使用select可以解决从管道取数据的阻塞问题

 //1.定义一个管道 10个数据int
 intChan := make(chan int, 10)
 for i := 0; i < 10; i++ {
  intChan <- i
 }
 //2.定义一个管道 5个数据string
 stringChan := make(chan string, 5)
 for i := 0; i < 5; i++ {
  stringChan <- "hello" + fmt.Sprintf("%d", i)
 }

 //传统的方法在遍历管道时，如果不关闭会阻塞而导致 deadlock

 //问题，在实际开发中，可能我们不好确定什么关闭该管道.
 //可以使用select 方式可以解决
 //label:
 for {
  select {
  //注意: 这里，如果intChan一直没有关闭，不会一直阻塞而deadlock
  //，会自动到下一个case匹配
  case v := <-intChan:
   fmt.Printf("从intChan读取的数据%d\n", v)
  case v := <-stringChan:
   fmt.Printf("从stringChan读取的数据%s\n", v)
  default:
   close(intChan)
   close(stringChan)
   fmt.Printf("都取不到了，不玩了, 程序员可以加入逻辑\n")
   return
   //break label
  }
 }
}
```

#### 斐波那契数列

```go
package main

import "fmt"

func fibonacci(c, quit chan int) {
 x, y := 1, 1
 for {
  select {
  case c <- x:
   x, y = y, x+y
  case <-quit:
   fmt.Println("quit")
   return
  }
 }
}
func main() {
 c := make(chan int)
 quit := make(chan int)
 go func() {
  for i := 0; i < 10; i++ {
   fmt.Println(<-c)
  }
  quit <- 0
 }()
 fibonacci(c, quit)
}
```

#### select的注意事项

select没有感知channel的关闭，这引出了2个问题：  
1）继续在关闭的通道上读，会读到通道传输数据类型的零值，  
2）继续在关闭的通道上写，将会panic。

> 某个通道关闭了，不再处理该通道，而是继续处理其他case，退出是等待所有的可读通道关闭  
> 我们需要使用select的一个特征：select不会在nil的通道上进行等待
> 这种情况，把只读通道设置为nil即可解决

```go
package main

import (
 "fmt"
)

func main() {
 intChan := make(chan int, 10)
 for i := 0; i < 10; i++ {
  intChan <- i
 }
//  close(intChan)

 stringChan := make(chan string, 5)
 for i := 0; i < 5; i++ {
  stringChan <- "hello" + fmt.Sprintf("%d", i)
 }
 close(stringChan)

 for {
  select {
  case v := <-intChan:
   fmt.Printf("从intChan读取的数据%d\n", v)
  case v, ok := <-stringChan:
   fmt.Printf("从stringChan读取的数据%s ,%t \n", v, ok)
   if !ok {
    stringChan = nil
   }
  default:
   close(intChan)
   // close(stringChan)
   fmt.Printf("都取不到了，不玩了, 程序员可以加入逻辑\n")
   return
  }
 }
}
```

## 7.关闭隧道

内建的`close`方法可以用来关闭channel。

```go
package main

import "fmt"

func main() {
 c := make(chan int, 10)
 c <- 1
 c <- 2
 close(c)
 // c <- 3 //=>关闭后在写入会报错
 //但是可以读取
 fmt.Println(<-c)
 fmt.Println(<-c)
 fmt.Println(<-c) //第3次已经没有值，所以返回 默认值0
 fmt.Println(<-c) //第4次已经没有值，所以返回 默认值0
}
```

#### for range 读取完后自动退出  

```go
c := make(chan int, 10)
c <- 1
c <- 2
close(c)
for i := range c {
 fmt.Println(i)
}
```

#### 通过i, ok := <-c可以查看Channel的状态，判断值是零值还是正常读取的值  

```go
c := make(chan int, 10)
close(c)
v, ok := <-c
fmt.Printf("%d, %t", v, ok) //0, false
```

**需要注意的细节：**

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 intChan := make(chan int, 10)
 intChan <- 10
 intChan <- 20
 
 //不写close直接deadlock! 错误，在别的协程中调用inChan只会阻塞
 close(intChan)

  num1, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num1, ok)

  num2, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num2, ok)

  num3, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num3, ok)
}

```

在新的协程中执行时，没有写close将会阻塞

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 intChan := make(chan int, 10)
 intChan <- 10
 intChan <- 20
 //close(intChan)

 go func(intChan chan int) {
  num1, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num1, ok)

  num2, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num2, ok)

  num3, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num3, ok)
 }(intChan)

 time.Sleep(time.Second * 5)
}
```

**执行结果**

```bash
$ go run main.go
num=10, ok=true
num=20, ok=true
```

写了close后才能正常的获取ok=false

```go
package main

import (
 "fmt"
 "time"
)

func main() {
 intChan := make(chan int, 10)
 intChan <- 10
 intChan <- 20
 close(intChan)

 go func(intChan chan int) {
  num1, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num1, ok)

  num2, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num2, ok)

  num3, ok := <-intChan
  fmt.Printf("num=%d, ok=%t\n", num3, ok)
 }(intChan)

 time.Sleep(time.Second * 5)
}
```

**执行结果**

```bash
$ go run main.go
num=10, ok=true
num=20, ok=true
num=0, ok=false
```

## 8.常用的开发模式

go协程开发时一般会有3个对象。

* 需要处理的资源  
* N个处理任务协程  
* 需要上述的协程都处理好了的判断逻辑  

看看下列例子
求1~80000中获取 2000倍数的数字  

## 8.1 普通的Channel阻塞模式

```go
package main

import (
 "fmt"
)

//给管道写入数据
func pushIntChan(intChan chan int) {
 for i := 1; i <= 80000; i++ {
  intChan <- i
 }
 close(intChan)
}

//计算2000的倍数
func calculation(intChan, resChan chan int, exitChan chan bool) {
 for {
  //如果 ok =false 说明管道已没数据，退出循环
  num, ok := <-intChan
  if !ok {
   //退出时给exitChan管道输入一个值，表示我这个协程结束。
   exitChan <- true
   break
  }

  //如果是2000的倍数放入到保存结果的channel中
  if num%2000 == 0 {
   resChan <- num
  }
 }
}

func main() {
 //要是用的协程数
 var n int = 4
 //创建3个管道
 intChan := make(chan int, 1000)  // 输入数据，资源
 resChan := make(chan int, 10000) // 计算素数结果的管道
 exitChan := make(chan bool, n)   // 判断所有的协程都结束的管道

 //输入数据
 go pushIntChan(intChan)

 //启用协程计算2000的倍数
 for i := 0; i < n; i++ {
  go calculation(intChan, resChan, exitChan)
 }

 // go func() {
 //等待所有的协程结束
 for i := 0; i < n; i++ {
  <-exitChan
 }
 close(resChan)
 close(exitChan)
 // }()

 for {
  v, ok := <-resChan
  if !ok {
   break
  }
  fmt.Println(v)
 }
}
```

上述问题是用Channel的阻塞实现了，所有协程的等待。  
使用`sync.WaitGroup`会实现的更轻松。  

## 8.2 sync.WaitGrou模式

```go
package main

import (
 "fmt"
 "sync"
)

//给管道写入数据
func pushIntChan(intChan chan int) {
 for i := 1; i <= 80000; i++ {
  intChan <- i
 }
 close(intChan)
}

//计算2000的倍数
func calculation(intChan, resChan chan int, wg *sync.WaitGroup) {
 for {
  //如果 ok =false 说明管道已没数据，退出循环
  num, ok := <-intChan
  if !ok {
   //退出时告诉wg已结束
   wg.Done()
   break
  }

  //如果是2000的倍数放入到保存结果的channel中
  if num%2000 == 0 {
   resChan <- num
  }
 }
}

func main() {
 //要是用的协程数
 var n int = 4
 //创建3个管道
 intChan := make(chan int, 1000)  // 输入数据，资源
 resChan := make(chan int, 10000) // 计算素数结果的管道

 wg := sync.WaitGroup{}
 wg.Add(n) // 判断所有的协程都结束

 //输入数据
 go pushIntChan(intChan)

 //启用协程计算2000的倍数
 for i := 0; i < n; i++ {
  go calculation(intChan, resChan, &wg)
 }

 //等待所有的协程结束
 wg.Wait()
 close(resChan)

 for v := range resChan {
  fmt.Println(v)
 }
}
```

## 9.Channel结构简介

#### 9.1 数据结构

**`runtime/chan.go`**  

```go
type hchan struct {
    qcount   uint           // 队列中剩余元素
    dataqsiz uint           // 队列长度，eg make(chan int64, 5), dataqsiz为5
    buf      unsafe.Pointer // 数据存储环形数组
    elemsize uint16         // 每个元素的大小
    closed   uint32         // 是否关闭 0 未关闭
    elemtype *_type         // 元素类型
    sendx    uint           // 发送者写入位置
    recvx    uint           // 接受者读数据位置
    recvq    waitq          // 接收者队列，保存正在读取channel的goroutine
    sendq    waitq          // 发送者队列，保存正在发送channel的goroutine
    lock     mutex          // 锁
}
```

**waitq 是双向链表，sudog 为 goroutine 的封装**  

```go
type waitq struct {
    first *sudog
    last  *sudog
}
```

**var intChan chan int = make(chan int, 6)**  

[图片备用地址](https://limingxie.github.io/images/go/channle/go_chan_struct.png)  
![go_chan_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/channel/go_chan_struct.png)

上图为一个长度为6，类型为int, 两个接收者，三个发送者的channel，当前接收者准备读数据的位置为0，发送者发送数据位置为4

> 一般情况下recvq和sendq至少有一个为空。  
> 只有一个例外，那就是同一个goroutine使用select语句向channel一边写数据,一边读数据。

<!-- 后续再了解 <https://learnku.com/articles/58480> -->

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
