---
author: "li_mingxie"
title: "【golang】垃圾回收gc简介(1)"
date: 2022-10-15T23:28:49+08:00
tags: [
    "gc",
]
categories: [
    "golang",
]
---

这一片简单的整理了 **`go的垃圾回收GC`** 。<!--more-->  

C和C++以后出现的高级编程语言大部分都有垃圾回收(Garbage Collection)机制。  
Go语言的垃圾回收GC是经历了3次比较大的改版。  
依次是:  
> 标记-清除(mark and sweep) => 三色标记法 => 三色标记法 + 混合屏障机制

----------------------------------------------

## 1.标记-清除(mark and sweep)方法

go的1.3版本之前采用的是标记-清除(mark and sweep)方法。  

### 1.1 基本原理

标记-清除(mark and sweep)方法就离不开STW(stop the world)。  
需要暂停所有程序，清理垃圾后，再次启用程序。  
如下图：  

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_01.png)  
![go_gc_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_01.png?x-oss-process=image/resize,w_450,m_lfit)

### 1.2 标记-清除(mark and sweep)方法的问题

1. STW过程需要暂停程序，出现程序卡顿现象。(主要问题)
2. 标记时需要扫描整改heap。
3. 清除数据会产生heap碎片。

----------------------------------------------

## 2.三色标记法

go的1.5版本开始使用了三色标记法。  

### 2.1 基本原理

三色标记法主要是把对象标记成三种颜色的对象。白色，灰色，黑色。  

* 白色：未搜索的对象，在回收周期开始时所有对象都是白色，在回收周期结束时所有的白色都是垃圾对象
* 灰色：正在搜索的对象，但是对象身上还有一个或多个引用没有扫描
* 黑色：已搜索完的对象，所有的引用已经被扫描完

```
灰色：对象还在标记队列中等待
黑色：对象已被标记，gcmarkBits 对应位为 1 -- 该对象不会在本次 GC 中被回收
白色：对象未被标记，gcmarkBits 对应位为 0 -- 该对象将会在本次 GC 中被清理
```

**三色标记法的五步进行过程：**  

1. 将所有对象标记为白色
2. 从根节点集合出发，将第一次遍历到的节点标记为灰色放入集合列表中
3. 遍历灰色集合，将灰色节点遍历到的白色节点标记为灰色，并把灰色节点标记为黑色
4. 循环这个过程
5. 直到灰色节点集合为空，回收所有的白色节点

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_02.png)  
![go_gc_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_02.png)

### 2.2 三色标记法的问题

**条件1:** 一个白色对象被黑色对象引用  
**条件2:** 灰色对象与它之间的可达关系的白色对象去掉关系。  

当同时满足这两个条件的时候，会清理有用的白色对象。  
如下图：  

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_06.png)  
![go_gc_06](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_06.png)

### 2.3 解决方法  

想预防如上问题，我们只需要破坏上述2钟情况的一种就可以。  

#### 2.3.1 强三色不变式

这方法是阻止条件1的产生。  

> 强制不允许一个白色对象被黑色对象引用。

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_04.png)  
![go_gc_04](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_04.png)

#### 2.3.2 弱三色不变式

这方法是阻止条件2的产生。  

> 白色对象存在其他灰色对象对他的引用，或者可达它的链路上有灰色对象时，黑色对象可以引用白色对象。

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_05.png)  
![go_gc_05](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_05.png)

----------------------------------------------

## 3.混合屏障机制

为了防止产生三色表示法的问题，程序进行时添加了额外的`屏障`。  

### 3.1 插入屏障

在A对象应用B对象的时候，B对象会被标记为灰色。=> `强三色不变式`  
为了性能只能是在堆heap内存中使用，栈stack内存是不适用。  

```go
//伪代码
添加下有对象(当前下游对象slot, 新下游对象ptr){

    标记灰色(新下游对象ptr)

    当前下游对象slot = 新下游对象ptr
}
```

> A.添加下有对象(B, nil) => A对象删除B对象的引用，B标记为灰色  
> A.添加下有对象(B, C) => A对象更换下游为C, B表示为灰色  

下图中因对象8是被堆Heap内存的对象4引用，所以可以直接走插入屏障。  
但是对象9是栈stack内存的对象1引用，所以不能走插入屏障。  
只能是最后到STW暂停保护后，确保不会被清除。  

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_07.png)  
![go_gc_07](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_07.png)

#### 插入屏障的不足

结束时，需要STW来重新扫描栈stack, 大约需要10~100ms的时间。  

### 3.2 删除屏障

被删除的对象，如果自身为灰色或白色，都被标记为灰色。=> `弱三色不变式`  

```go
//伪代码
添加下有对象(当前下游对象slot, 新下游对象ptr){

    if(当前下游对象slot == 灰色 || 当前下游对象slot == 白色){
        标记灰色(新下游对象ptr)
    }

    当前下游对象slot = 新下游对象ptr
}
```

> A.添加下有对象(nil, B) => 给A新添加下游对象，把B标记为灰色  
> A.添加下有对象(C, B) => 下游对象C，更新为B，把B标记为灰色  

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_08.png)  
![go_gc_08](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_08.png)

#### 删除屏障的不足

回收精度低:  
一个对象即使被删除了最后一个指向它的指针也依旧可以存活这一轮，  
到了下一轮GC中才能被清除掉。  

## 4.Go语言的GC

go的1.8版本开始是三色标记法 + 混合写屏障hybrid write barrier。  
需遵守如下规则:  

1. GC开始将栈上的对象全部扫描并标记为黑色(之后不在进行第二次扫描，无需STW)
2. GC期间，任何在栈上的创建的对象，均为黑色
3. 被删除的对象标记为灰色
4. 被添加的对象标记为灰色

> 变形的弱三色不变式(结合了插入，删除写屏障的优点)

```go
//伪代码
writePointer(slot, ptr):
    shade(*slot)
    if current stack is grey:
        shade(ptr)
    *slot = ptr
```

下面分析几个特殊场景，以便了解流程。

#### 4.1 场景1: 对象被一个堆对象删除引用，成为栈对象的下游

```
//前提：堆对象4 -> 对象7 = 对象7; //对象7 被 对象4引用
栈对象1 -> 对象7 = 堆对象7; //将堆对象7 挂在 栈对象1 下游
堆对象4 -> 对象7 = null; //对象4 删除引用 对象7
```

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_09.png)  
![go_gc_09](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_09.png)

#### 4.2 场景2: 对象被一个栈对象删除引用，成为另一个栈对象的下游

```
new 栈对象9;
对象9 -> 对象3 = 对象3;  //将栈对象3 挂在 栈对象9 下游
对象2 -> 对象3 = null;  //对象2 删除引用 对象3
```

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_10.png)  
![go_gc_10](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_10.png)

#### 4.3 场景3: 对象被一个堆对象删除引用，成为另一个堆对象的下游

```
堆对象10 -> 对象7 = 堆对象7;    //将堆对象7 挂在 堆对象10 下游
堆对象4 -> 对象7 = null;       //对象4 删除引用 对象7
```

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_11.png)  
![go_gc_11](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_11.png)

#### 4.4 场景4: 对象从一个栈对象删除引用，成为另一个堆对象的下游

```
栈对象1 -> 对象2 = null;    //对象1 删除引用 对象2
堆对象4 -> 对象2 = 栈对象2； //对象4 添加下游 栈对象2
堆对象4 -> 对象7 = null;    //对象4 删除引用 对象7
```

[图片备用地址](https://limingxie.github.io/images/go/gc/go_gc_12.png)  
![go_gc_12](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/gc/go_gc_12.png)

<!-- ## 5.垃圾回收阶段

1. 清理终止阶段；
暂停程序，所有的处理器在这时会进入安全点（Safe point）；
如果当前垃圾收集循环是强制触发的，我们还需要处理还未被清理的内存管理单元；
2. 标记阶段；
将状态切换至 _GCmark、开启写屏障、用户程序协助（Mutator Assists）并将根对象入队；
恢复执行程序，标记进程和用于协助的用户程序会开始并发标记内存中的对象，写屏障会将被覆盖的指针和新指针都标记成灰色，而所有新创建的对象都会被直接标记成黑色；
开始扫描根对象，包括所有 Goroutine 的栈、全局对象以及不在堆中的运行时数据结构，扫描 Goroutine 栈期间会暂停当前处理器；
依次处理灰色队列中的对象，将对象标记成黑色并将它们指向的对象标记成灰色；
使用分布式的终止算法检查剩余的工作，发现标记阶段完成后进入标记终止阶段；
3. 标记终止阶段；
暂停程序、将状态切换至_GCmarktermination 并关闭辅助标记的用户程序；
清理处理器上的线程缓存；
4. 清理阶段；
将状态切换至 _GCoff 开始清理阶段，初始化清理状态并关闭写屏障；
恢复用户程序，所有新创建的对象会标记成白色；
后台并发清理所有的内存管理单元，当 Goroutine 申请新的内存管理单元时就会触发清理；
运行时虽然只会使用_GCoff、_GCmark 和_GCmarktermination 三个状态表示垃圾收集的全部阶段，但是在实现上却复杂很多，本节将按照垃圾收集的不同阶段详细分析其实现原理。 -->

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
