---
author: "li_mingxie"
title: "【go笔记】指针, uintptr, unsafe.Pointer"
date: 2921-07-27T07:28:49+08:00
tags: [
    "unsafe",
    "uintptr",
    "pointer",
]
categories: [
    "Go",
    "golang",
]
---

Go中的指针及与指针对指针的操作主要有以下三种：

一普通的指针类型，例如 var intptr *T，定义一个T类型指针变量。

二内置类型uintptr，本质是一个无符号的整型，它的长度是跟平台相关的，它的长度可以用来保存一个指针地址。

三是unsafe包提供的Pointer，表示可以指向任意类型的指针。

*类型:普通指针类型，用于传递对象地址，不能进行指针运算。
unsafe.Pointer:通用指针类型，用于转换不同类型的指针，不能进行指针运算，不能读取内存存储的值（必须转换到某一类型的普通指针）。unsafe.Pointer 是桥梁，可以让任意类型的指针实现相互转换，也可以将任意类型的指针转换为 uintptr 进行指针运算。
uintptr:用于指针运算，GC 不把 uintptr 当指针，uintptr 无法持有对象。uintptr 类型的目标会被回收。比如要在某个指针地址上加上一个偏移量，做完加减法后，转换成Pointer，通过*操作，取值，修改值，随意。

### 1. 普通指针(*T)


### 2. uintptr


```
type uintptr

type uintptr uintptr

uintptr is an integer type that is large enough to hold the bit pattern of any pointer.
uintptr是一个能足够容纳指针位数大小的整数类型
```


### 3. unsafe.Pointer

```
type Pointer *ArbitraryType

- A pointer value of any type can be converted to a Pointer.
- A Pointer can be converted to a pointer value of any type.
- A uintptr can be converted to a Pointer.
- A Pointer can be converted to a uintptr.

- 任何类型的指针都可以被转化为Pointer
- Pointer可以被转化为任何类型的指针
- uintptr可以被转化为Pointer
- Pointer可以被转化为uintptr
```

Pointer represents a pointer to an arbitrary type. There are four special operations
available for type Pointer that are not available for other types:    //  Pointer代表了一个任意类型的指针。Pointer类型有四种特殊的操作是其他类型不能使用的:
   - A pointer value of any type can be converted to a Pointer.       //  任意类型的指针可以被转换为Pointer
   - A Pointer can be converted to a pointer value of any type.       //  Pointer可以被转换为任务类型的值的指针
   - A uintptr can be converted to a Pointer.                         //  uintptr可以被转换为Pointer
   - A Pointer can be converted to a uintptr.                         //  Pointer可以被转换为uintptr
Pointer therefore allows a program to defeat the type system and read and write
arbitrary memory. It should be used with extreme care.                //  因此Pointer允许程序不按类型系统的要求来读写任意的内存，应该非常小心地使用它。

所以unsafe.Pointer做的主要是用来进行桥接，用于不同类型的指针进行互相转换。

在任何情况下，结果都必须继续指向原分配的对象。


1.将unsafe.Pointer转换为uintptr

2.对uintptr执行算术运算

3.将uintptr转换回unsafe.Pointer,然后访问uintptr地址指向的对象

### 总结

需要用key-value结构的话就用映射(map)。  
一般不会用到数组结构，大部分情况下都会用比较方便的切片(slice)。  
使用切片(slice)的时候，需了解容量及自动扩容的机制(会增加1倍的容量，头部地址不变，值的内存地址会有变化)。  
切片(slice)当做参数，或是相互赋值的时候会出现共用值内存地址的情况，这时需要注意一下有没有数据被串改的可能性。  


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`






Go 普通指针类型、unsafe.Pointer、uintptr的区别与联系
unsafe 是不安全的，所以我们应该尽可能少的使用它，比如内存的操纵，这是绕过 Go 本身设计的安全机制的，不当的操作，可能会破坏一块内存，而且这种问题非常不好定位。

当然必须的时候我们可以使用它，比如底层类型相同的数组之间的转换；比如使用 sync/atomic 包中的一些函数时；还有访问 struct 的私有字段时；该用还是要用，不过一定要慎之又慎。

Golang指针
*类型:普通指针类型，用于传递对象地址，不能进行指针运算。
unsafe.Pointer:通用指针类型，用于转换不同类型的指针，不能进行指针运算，不能读取内存存储的值（必须转换到某一类型的普通指针）。unsafe.Pointer 是桥梁，可以让任意类型的指针实现相互转换，也可以将任意类型的指针转换为 uintptr 进行指针运算。
uintptr:用于指针运算，GC 不把 uintptr 当指针，uintptr 无法持有对象。uintptr 类型的目标会被回收。比如要在某个指针地址上加上一个偏移量，做完加减法后，转换成Pointer，通过*操作，取值，修改值，随意。
官方文档对unsafe.Pointer的描述的四个规则：
（1）任何类型的指针都可以被转化为Pointer
（2）Pointer可以被转化为任何类型的指针
（3）uintptr可以被转化为Pointer
（4）Pointer可以被转化为uintptr

指针类型转换
Go 语言在设计的时候，为了编写方便、效率高以及降低复杂度，被设计成为一门强类型的静态语言。强类型意味着一旦定义了，它的类型就不能改变了；静态意味着类型检查在运行前就做了。

同时为了安全的考虑，Go 语言是不允许两个指针类型进行转换的。

一般使用 *T 作为一个指针类型，表示一个指向类型T变量的指针。为了安全的考虑，两个不同的指针类型不能相互转换，比如 *int 不能转为 *float64

import "fmt"

func main() {
	i := 10
	ip := &i

	var fp *float64 = (*float64)(ip)

	fmt.Println(fp)
}

以上代码我们在编译的时候，会提示 cannot convert ip (type *int) to type *float64，也就是不能进行强制转型

unsafe.pointer用于普通指针类型转换
接以上，

func main() {
	i := 10
	ip := &i

	var fp *float64 = (*float64)(unsafe.Pointer(ip))

	*fp = *fp * 3

	fmt.Println(i)
}

以上示例，我们可以把 *int 转为 *float64，并且我们尝试了对新的 *float64 进行操作，打印输出 i，就会发现 i 的值同样被改变。
的
前面两个规则我们刚刚已经演示了，主要用于 *T1 和 *T2 之间的转换，那么最后两个规则是做什么的呢？*我们都知道 T 是不能计算偏移量的，也不能进行计算，但是 uintptr 可以，所以我们可以把指针转为 uintptr 再进行偏移计算，这样我们就可以访问特定的内存了，达到对不同的内存读写的目的。

下面我们以通过指针偏移修改Struct结构体内的字段为例，来演示 uintptr 的用法

func main() {
	u := new(user)
	fmt.Println(*u)

	pName := (*string)(unsafe.Pointer(u))
	*pName = "张三"

	pAge := (*int)(unsafe.Pointer(uintptr(unsafe.Pointer(u)) + unsafe.Offsetof(u.age)))
	*pAge = 20

	fmt.Println(*u)
}

type user struct {
	name string
	age  int
}

以上我们通过内存偏移的方式，定位到我们需要操作的字段，然后改变他们的值。

第一个修改 user 的 name 值的时候，因为 name 是第一个字段，所以不用偏移，我们获取 user 的指针，然后通过 unsafe.Pointer 转为 *string 进行赋值操作即可。

第二个修改 user 的 age 值的时候，因为 age 不是第一个字段，所以我们需要内存偏移，内存偏移牵涉到的计算只能通过 uintptr，所我们要先把 user 的指针地址转为 uintptr，然后我们再通过 unsafe.Offsetof(u.age) 获取需要偏移的值，进行地址运算(+)偏移即可。

现在偏移后，地址已经是 user 的 age 字段了，如果要给它赋值，我们需要把 uintptr 转为 *int 才可以。所以我们通过把 uintptr 转为 unsafe.Pointer，再转为 *int 就可以操作了。

这里我们可以看到，我们第二个偏移的表达式非常长，但是也千万不要把他们分段，不能像下面这样。

temp := uintptr(unsafe.Pointer(u)) + unsafe.Offsetof(u.age)
pAge := (*int)(unsafe.Pointer(temp))
*pAge = 20

逻辑上看，以上代码不会有什么问题，但是这里会牵涉到 GC，如果我们的这些临时变量被 GC，那么导致的内存操作就错了，我们最终操作的，就不知道是哪块内存了，会引起莫名其妙的问题。