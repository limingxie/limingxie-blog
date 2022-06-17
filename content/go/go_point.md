---
author: "li_mingxie"
title: "【go笔记】指针, uintptr, unsafe.Pointer"
date: 2022-03-10T07:28:49+08:00
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

今天简单整理一下以下三种指针: 普通指针(*T), uintptr, unsafe.Pointer<!--more-->

### 1. 普通指针(*T)

普通指针类型，用于传递对象地址，不能进行指针运算。
可以用 &(取地址) *(根据地址取值)。  
这一块都很熟悉了，所以不在做过多的解释了。

### 2. uintptr

uintptr是一个无符号的整型，它可以保存一个指针地址。  
它可以进行指针运算。  
uintptr无法持有对象, GC不把uintptr当指针, 所以uintptr类型的目标会被回收。  
想取值需要转成unsafe.Pointer后, 需再转到相对应的指针类型。  

uintptr 在 builtin的package里。源代码中是这么解释的。  

``` 
package builtin

//uintptr is an integer type that is large enough to hold the bit pattern of any pointer.
//uintptr是一个能足够容纳指针位数大小的整数类型
type uintptr uintptr
```

### 3. unsafe.Pointer

unsafe.Pointer可以指向任意类型的指针。  
不能进行指针运算，不能读取内存存储的值(想读取的话需要转成相对应类型的指针)。  
它是桥梁，让任意类型的指针实现相互转换, 也可以转换成uintptr 进行指针运算。  

Pointer是在unsafe的package里。  
源代码中的解释超级长，所以在说明文档中把部分内容拿来了。

```
type Pointer *ArbitraryType

Pointer represents a pointer to an arbitrary type. There are four special operations
available for type Pointer that are not available for other types:    //  Pointer代表了一个任意类型的指针。Pointer类型有四种特殊的操作是其他类型不能使用的:
   - A pointer value of any type can be converted to a Pointer.       //  任意类型的指针可以被转换为Pointer
   - A Pointer can be converted to a pointer value of any type.       //  Pointer可以被转换为任务类型的值的指针
   - A uintptr can be converted to a Pointer.                         //  uintptr可以被转换为Pointer
   - A Pointer can be converted to a uintptr.                         //  Pointer可以被转换为uintptr
Pointer therefore allows a program to defeat the type system and read and write
arbitrary memory. It should be used with extreme care.                //  因此Pointer允许程序不按类型系统的要求来读写任意的内存，应该非常小心地使用它。
```

一般的指针运算会走一下三个步骤。
1.将unsafe.Pointer转换为uintptr => 2.对uintptr执行算术运算 => 3.将uintptr转换回unsafe.Pointer,然后转成访问指向的对象的指针类型。

下面看看简单的测试。

### 简单的实践

可以看看下面测试, 数组和struct是可以使用指针偏移指向下一个元素。

```go
 a := [10]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
 b := unsafe.Pointer(uintptr(unsafe.Pointer(&a[0])) + 9*unsafe.Sizeof(a[0]))

 // b是 unsafe.Pointer 所以可转任意指针，转成(*int)指针后在取值
 fmt.Printf("b: %v, unsafe.Sizeof(a[0]): %d\n", *(*int)(b), unsafe.Sizeof(a[0])) //b: 9, unsafe.Sizeof(a[0]): 8

 c := unsafe.Pointer(uintptr(unsafe.Pointer(&a)) + uintptr(16)) //int是8位长度 所以16 等于 16/8 挪动了2位，所以下面结果是2
 fmt.Printf("c: %v\n", *(*int)(c))                              //c: 2

 user := user{id: 1, age: 10, name: "user1"}
 namePointer := unsafe.Pointer(uintptr(unsafe.Pointer(&user)) + unsafe.Offsetof(user.name))

 //这也一样 name是 unsafe.Pointer 所以可转任意指针，转成(*string)指针后在取值
 fmt.Printf("name: %v\n", *(*string)(namePointer)) //name: user1
```

再看看slice和map相关的简单测试。

```go
 //因slice的结构是 => |ptr|len|cap
 s := make([]int, 5, 10)
 var Len = *(*int)(unsafe.Pointer(uintptr(unsafe.Pointer(&s)) + uintptr(8))) //挪一个位置是Len
 fmt.Println(Len, len(s))                                                    // 5 5

 var Cap = *(*int)(unsafe.Pointer(uintptr(unsafe.Pointer(&s)) + uintptr(16))) //挪二个位置是CAP
 fmt.Println(Cap, cap(s))                                                     // 10 10

 mp := make(map[string]int)
 mp["a"] = 11
 mp["b"] = 22

 //因map结构中第一个是元素个数，所以可以直接转成len
 count := **(**int)(unsafe.Pointer(&mp))
 fmt.Println(count, len(mp)) // 2 2
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
