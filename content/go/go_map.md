---
author: "li_mingxie"
title: "【go笔记】goroutine调度器的GMP模型的简介"
date: 2022-03-21T07:28:49+08:00
tags: [
    "go",
    "golang",
    "goroutine",
    "channel",
]
categories: [
    "Go",
    "golang",
]
---

说起go语言，离不开goroutine。  
之前使用go语言开发的时候，也没有用过太多的多线程模式。  
趁着些天了解一下GMP模型G(goroutine) M(thread) P(Processor)。

## 1.GMP模型

#### G -> goroutine

Go中，协程被称为goroutine，一个goroutine只占几KB。  
而且调度也很灵活(是通过runtime调度的)。

#### P -> Processor

它包含了运行goroutine的资源，如果线程想运行goroutine，必须先获取P，P中还包含了可运行的G队列。

#### M -> thread

每个M都代表了1个内核线程，OS调度器负责把内核线程分配到CPU的核上执行。

我们看看下图，goroutine是如何运行的:

[图片备用地址](https://limingxie.github.io/images/go/goroutine/gmp_1.png)  
![gmp](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/goroutine/gmp_1.png?x-oss-process=image/resize,w_650,m_lfit)



全局队列（Global Queue）：存放等待运行的 G。
P 的本地队列：同全局队列类似，存放的也是等待运行的 G，存的数量有限，不超过 256 个。新建 G’时，G’优先加入到 P 的本地队列，如果队列满了，则会把本地队列中一半的 G 移动到全局队列。
P 列表：所有的 P 都在程序启动时创建，并保存在数组中，最多有 GOMAXPROCS(可配置) 个。
M：线程想运行任务就得获取 P，从 P 的本地队列获取 G，P 队列为空时，M 也会尝试从全局队列拿一批 G 放到 P 的本地队列，或从其他 P 的本地队列偷一半放到自己 P 的本地队列。M 运行 G，G 执行之后，M 会从 P 获取下一个 G，不断重复下去。

1、P 的数量：
由启动时环境变量 $GOMAXPROCS 或者是由 runtime 的方法 GOMAXPROCS() 决定。这意味着在程序执行的任意时刻都只有 $GOMAXPROCS 个 goroutine 在同时运行。
2、M 的数量:
go 语言本身的限制：go 程序启动时，会设置 M 的最大数量，默认 10000. 但是内核很难支持这么多的线程数，所以这个限制可以忽略。
runtime/debug 中的 SetMaxThreads 函数，设置 M 的最大数量
一个 M 阻塞了，会创建新的 M。


P 和 M 何时会被创建
1、P 何时创建：在确定了 P 的最大数量 n 后，运行时系统会根据这个数量创建 n 个 P。
2、M 何时创建：没有足够的 M 来关联 P 并运行其中的可运行的 G。比如所有的 M 此时都阻塞住了，而 P 中还有很多就绪任务，就会去寻找空闲的 M，而没有空闲的，就会去创建新的 M。


1）work stealing 机制
​ 当本线程无可运行的 G 时，尝试从其他线程绑定的 P 偷取 G，而不是销毁线程。
2）hand off 机制
​ 当本线程因为 G 进行系统调用阻塞时，线程释放绑定的 P，把 P 转移给其他空闲的线程执行。
利用并行：GOMAXPROCS 设置 P 的数量，最多有 GOMAXPROCS 个线程分布在多个 CPU 上同时运行。GOMAXPROCS 也限制了并发的程度，比如 GOMAXPROCS = 核数/2，则最多利用了一半的 CPU 核进行并行。
抢占：在 coroutine 中要等待一个协程主动让出 CPU 才执行下一个协程，在 Go 中，一个 goroutine 最多占用 CPU 10ms，防止其他 goroutine 被饿死，这就是 goroutine 不同于 coroutine 的一个地方。
全局 G 队列：在新的调度器中依然有全局 G 队列，但功能已经被弱化了，当 M 执行 work stealing 从其他 P 偷不到 G 时，它可以从全局 G 队列获取 G。

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
