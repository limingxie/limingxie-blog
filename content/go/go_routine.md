---
author: "li_mingxie"
title: "【go笔记】goroutine调度器的GMP模型简介"
date: 2022-03-23T07:28:49+08:00
tags: [
    "go",
    "golang",
    "goroutine",
    "channel",
]
categories: [
    "golang",
]
---

说起go语言，离不开goroutine。  
之前使用go语言开发的时候，也没多少机会用到goroutine。  
趁这些天了解一下GMP模型G(goroutine) M(thread) P(Processor)。
<!--more-->
## 1.GMP模型

#### G -> goroutine

Go中，协程被称为goroutine，一个goroutine只占几KB。  
而且调度也很灵活(是通过runtime调度的)。

#### P -> Processor

它包含了运行goroutine的资源，如果线程想运行goroutine，必须先获取P，P中还包含了可运行的G队列。

#### M -> thread

每个M都代表了1个内核线程，OS调度器负责把内核线程分配到CPU的核上执行。

```
协程是程序级别的，  
协程是通过runtime调度器的Processor分配到thread。

线程thread是操作系统级别的。  
thread是通过操作系统OS调度器分配到CPU上执行内容。  
```

看看下图：

[图片备用地址](https://limingxie.github.io/images/go/goroutine/gmp_1.png)  
![gmp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/goroutine/gmp_1.png?x-oss-process=image/resize,w_650,m_lfit)

我们从下往上看每个节点的作用:

> **CPU**:=> 线程是在CPU中执行。每个CPU一次只能处理一个线程thread。  
> **M(thread)**:=> M从P本地队列中获取G执行，执行后从P获取下一个G, 不断的重复下去。  
> **OS调度器**:=> 管理M和CPU之间的调度。  
> **P(Processor)**:=> P是M与G之间的协调。所有的P都在程序启动时创建，最多有GOMAXPROCS(可配置)个。P和M是1:1的关系。  
> **goroutine调度器**:=> 管理P和G之间的调度。  
> **P的本地队列**:=> 存放等待运行的G, 新建G时会优先加入到P的本地队列, 如果队列满了, 则会把本地队列中一半的G移动到全局队列。  
> **全局队列(Global Queue)**:=> 存放等待运行的G。  

## 2.Go调度器调度过程解析

下面分析一下调度器调度的过程。

#### ◆func()调度过程  

假设有一个 go func(), 简单的模拟一下调度的过程。

1. => go func()  
2. => 创建G  
3. => G进入P的本地队列(满了进入全局队列)  
4. => 和P有关联的M通过P获取G(如空的话去别的本地列或全局队列里获取)  
5. => M在CPU中执行。

#### ◆main()调度过程

看下一段代码执行的过程

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello world")
}
```

1. => runtime 创建最初的线程 M0 和 G0，并把2者关联。
2. => 调度器初始化：初始化 M0、栈、垃圾回收，以及创建和初始化由 GOMAXPROCS设定个数的P和P列表。
3. => 代码经过编译后，runtime.main会调用main.main，程序启动时会为runtime.main创建goroutine，然后把main goroutine加入到P的本地队列。
4. => 上述操作中，M0已经绑定了P0，会从P的本地队列获取G，获取到 main goroutine。
5. => M根据G中的栈信息和调度信息设置运行环境后, M运行G。
6. => 执行完G后退出，再次回到M获取可运行的G，这样重复下去，直到main.main退出，  
runtime.main执行Defer和Panic处理，或调用runtime.exit退出程序。  
runtime.main的goroutine 运行是调度器的真正开始，直到runtime.main结束。

这里补充说明一下M0和G0

M0 是启动程序后的编号为0的主线程，M0对应的实例会在全局变量 runtime.m0 中，不需要在heap上分配，  
M0 负责执行初始化操作和启动第一个 G， 在之后M0就和其他的 M 一样了。

G0 是每次启动一个M都会第一个创建的 gourtine，G0仅用于负责调度的G，G0不指向任何可执行的函数，每个M都会有一个自己的G0。  
在调度或系统调用时会使用G0的栈空间，全局变量的G0是M0的G0。

#### ◆深入解剖调度过程

看下图：  
**刚开始的时候G0会协调要执行的G协程，执行G1的时候有又需要执行G2，这时候优先会放到自己的P本地队列，**
**G1执行完后，G0会依次协调执行G2。**

[图片备用地址](https://limingxie.github.io/images/go/goroutine/goroutine_01.png)  
![gmp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/goroutine/goroutine_01.png)

**执行的过程中M如果阻塞了，那P会寻找下一个可执行的M，继续执行下一个G。**  

[图片备用地址](https://limingxie.github.io/images/go/goroutine/goroutine_02.png)  
![gmp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/goroutine/goroutine_02.png?x-oss-process=image/resize,w_900,m_lfit)

**有2个以上P的时候：**
**P2里的本地队列已经没有可执行G了，他就从全局队列中获取可执行G。**  
**全局队列里没有可执行G时，会从别的P本地队列中偷取可执行G来执行。**  
**没有可执行G就会进入自选状态等待下一个G。**  

[图片备用地址](https://limingxie.github.io/images/go/goroutine/goroutine_03.png)  
![gmp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/goroutine/goroutine_03.png)

## 3.结语

了解这些底层逻辑是为了更好的时候这些功能，  
了解这些goroutine轻便的调度功能，后续写代码的时候多多少少会有一些帮助吧？  

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`

<!-- 
1、P 的数量： 由启动时环境变量 $GOMAXPROCS 或者是由 runtime 的方法 GOMAXPROCS() 决定。这意味着在程序执行的任意时刻都只有 $GOMAXPROCS 个 goroutine 在同时运行。 2、M 的数量: go 语言本身的限制：go 程序启动时，会设置 M 的最大数量，默认 10000. 但是内核很难支持这么多的线程数，所以这个限制可以忽略。 runtime/debug 中的 SetMaxThreads 函数，设置 M 的最大数量 一个 M 阻塞了，会创建新的 M。 

1、我们通过 go func () 来创建一个 goroutine；
​ 2、有两个存储 G 的队列，一个是局部调度器 P 的本地队列、一个是全局 G 队列。新创建的 G 会先保存在 P 的本地队列中，如果 P 的本地队列已经满了就会保存在全局的队列中；
​ 3、G 只能运行在 M 中，一个 M 必须持有一个 P，M 与 P 是 1：1 的关系。M 会从 P 的本地队列弹出一个可执行状态的 G 来执行，如果 P 的本地队列为空，就会想其他的 MP 组合偷取一个可执行的 G 来执行；
​ 4、一个 M 调度 G 执行的过程是一个循环机制；
​ 5、当 M 执行某一个 G 时候如果发生了 syscall 或则其余阻塞操作，M 会阻塞，如果当前有一些 G 在执行，runtime 会把这个线程 M 从 P 中摘除 (detach)，然后再创建一个新的操作系统的线程 (如果有空闲的线程可用就复用空闲线程) 来服务于这个 P；
​ 6、当 M 系统调用结束时候，这个 G 会尝试获取一个空闲的 P 执行，并放入到这个 P 的本地队列。如果获取不到 P，那么这个线程 M 变成休眠状态， 加入到空闲线程中，然后这个 G 会被放入全局队列中。

package main

import "fmt"

func main() {
    fmt.Println("Hello world")
}
接下来我们来针对上面的代码对调度器里面的结构做一个分析。

也会经历如上图所示的过程：

1.runtime 创建最初的线程 m0 和 goroutine g0，并把 2 者关联。
2.调度器初始化：初始化 m0、栈、垃圾回收，以及创建和初始化由 GOMAXPROCS 个 P 构成的 P 列表。
3.示例代码中的 main 函数是 main.main，runtime 中也有 1 个 main 函数 ——runtime.main，代码经过编译后，runtime.main 会调用 main.main，程序启动时会为 runtime.main 创建 goroutine，称它为 main goroutine 吧，然后把 main goroutine 加入到 P 的本地队列。
4.启动 m0，m0 已经绑定了 P，会从 P 的本地队列获取 G，获取到 main goroutine。
5.G 拥有栈，M 根据 G 中的栈信息和调度信息设置运行环境
6.M 运行 G
7.G 退出，再次回到 M 获取可运行的 G，这样重复下去，直到 main.main 退出，runtime.main 执行 Defer 和 Panic 处理，或调用 runtime.exit 退出程序。
调度器的生命周期几乎占满了一个 Go 程序的一生，runtime.main 的 goroutine 执行之前都是为调度器做准备工作，runtime.main 的 goroutine 运行，才是调度器的真正开始，直到 runtime.main 结束而结束。

-->
