---
author: "li_mingxie"
title: "【go笔记】map结构的简介"
date: 2022-03-11T07:28:49+08:00
tags: [
    "go",
    "golang",
    "slice",
    "make",
]
categories: [
    "Go",
    "golang",
]
---

go的hashtable是用map实现。  
今天简单的整理了map的结构。  

### 1.map

**切片(slice)是数组的一个引用**，因此切片是引用类型。但自身是结构体，值拷贝传递。  
看看下图就应该能明白，为什么说是引用类型，可又说自身又是结构体。  

[图片备用地址](https://limingxie.github.io/images/go/slice/slice_code_1.png)  
![slice_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/slice/slice_code_1.png?x-oss-process=image/resize,h_200,m_lfit))

Slice 的数据结构可以如下定义:

```go
type slice struct {
    array unsafe.Pointer  //指向第一个元素的地址
    len   int
    cap   int
}
```

### 2.切片(slice)的内存布局

我们看看下图切片(slice)的内存布局是如何实现的。

[图片备用地址](https://limingxie.github.io/images/go/slice/slice_code_2.png)  
![slice_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/slice/slice_code_2.png?x-oss-process=image/resize,h_200,m_lfit)

如图所示，切片指向数组的第2个元素，长度是2，留了4个容量。  
切片指向了23元素，长度是2 所以切片指的是[23,37]，再往后留了2个预留空间。
可以看下图:

[图片备用地址](https://limingxie.github.io/images/go/slice/slice_code_3.png)  
![slice_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/slice/slice_code_3.png?x-oss-process=image/resize,h_200,m_lfit)

如果上述结构了解了，下面的这个现象就好理解了。

```go
    data := []int{0, 1, 2, 3, 4, 5}

	s := data[2:4]
	s[0] += 100
	s[1] += 200

	fmt.Println(s)    //[102 203]
	fmt.Println(data) //[0 1 102 203 4 5]

	s = append(s, 300) // 这个操作会影响 s1,data1 值， 因为都看同一个值

	fmt.Println(s)    //[102 203 300]
	fmt.Println(data) //[0 1 102 203 300 5]
```

### 3.切片(slice)的append

给切片(slice)添加元素需要用append。  
当切片(slice)的cap有剩余空间时是直接添加到下一个位置，  
如果没有剩余空间，会扩容2倍的空间(高于1024 扩容 1.25倍)。
如图下图：

[图片备用地址](https://limingxie.github.io/images/go/slice/slice_code_4.png)  
![slice_struct](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/go/slice/slice_code_4.png?x-oss-process=image/resize,h_200,m_lfit)

### 4.切片(slice)代码实验

切片(slice)的几个特性直接写了代码了。
可以尝试着看下面的代码，了解切片(slice)的一些特性和用法。  

```go

```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`