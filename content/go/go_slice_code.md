---
author: "li_mingxie"
title: "【go笔记】切片(slice)结构的简介"
date: 2022-03-12T07:28:49+08:00
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

我们java里有个ArrayList， 那go有切片(slice)。  
看起来有点像，但是了解切片(slice)结构后，会发现这其实是个两个不同的结构。  
下面简单的整理了切片(slice)的结构。

### 1.切片(slice)

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
//slice声明方式
func sliceTest() {
	//1.声明切片
	var s1 []int
	if s1 == nil {
		fmt.Println("是空")
	} else {
		fmt.Println("不是空")
	}
	// 2.:=
	s2 := []int{}
	// 3.make()
	var s3 []int = make([]int, 0)
	fmt.Println(s1, s2, s3)
	// 4.初始化赋值
	// make([]type, len, cap)
	var s4 []int = make([]int, 0, 0)
	fmt.Println(s4)
	s5 := []int{1, 2, 3}
	fmt.Println(s5)
	// 5.从数组切片
	arr := [5]int{1, 2, 3, 4, 5}
	var s6 []int
	// 前包后不包
	s6 = arr[1:4]
	fmt.Println(s6)
}

//slice截取
func sliceTest1() {
	var arr = []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	var slice0 []int = arr[2:8]
	var slice1 []int = arr[0:6]        //可以简写为 var slice []int = arr[:end]
	var slice2 []int = arr[5:10]       //可以简写为 var slice[]int = arr[start:]
	var slice3 []int = arr[0:len(arr)] //var slice []int = arr[:]
	var slice4 = arr[:len(arr)-1]

	fmt.Printf("arr: %v\n", arr)
	fmt.Printf("slice0: %v\n", slice0)
	fmt.Printf("slice1: %v\n", slice1)
	fmt.Printf("slice2: %v\n", slice2)
	fmt.Printf("slice3: %v\n", slice3)
	fmt.Printf("slice4: %v\n", slice4)
}

//slice截取的值，会指向原有的值。
func sliceTest2() {
	data := []int{0, 1, 2, 3, 4, 5}

	s := data[2:4]
	s[0] += 100
	s[1] += 200

	fmt.Println(s)    //[102 203]
	fmt.Println(data) //[0 1 102 203 4 5]

	//需要注意的现象
	data1 := make([]int, 6, 30)
	for i := 0; i < 6; i++ {
		data1[i] = i
	}
	s1 := data1[0:2]
	s1 = append(s1, 10) // 这个操作会影响 s1,data1 值， 因为都看同一个值

	fmt.Printf("%v\n", s1)    //[0 1 10]
	fmt.Printf("%v\n", data1) //[0 1 10 3 4 5]

	data2 := make([]int, 6, 30)
	for i := 0; i < 6; i++ {
		data2[i] = i
	}
	s2 := data2[3:] //s2的值是 指向s2[0] 所以s2和data2的指针地址不一样，但是s2[1],和data2[4] 指向的都是同一个内存空间。
	s2[0] = 10
	// s2 = append(s2, 10)

	fmt.Printf("%v, %v, %v, %p\n", s2, len(s2), cap(s2), &s2[1])             //[10 4 5], 3, 27, 0xc000108110
	fmt.Printf("%v, %v, %v, %p\n", data2, len(data2), cap(data2), &data2[4]) //[0 1 2 10 4 5], 6, 30, 0xc000108110

}

//声明slice的时候 len 和 cap 的关系
func sliceTest3() {
	s1 := []int{0, 1, 2, 3, 8: 100} // 通过初始化表达式构造，可使用索引号。
	fmt.Println(s1, len(s1), cap(s1))

	s2 := make([]int, 6, 8) // 使用 make 创建，指定 len 和 cap 值。
	fmt.Println(s2, len(s2), cap(s2))

	s3 := make([]int, 6) // 省略 cap，相当于 cap = len。
	fmt.Println(s3, len(s3), cap(s3))
}

func sliceTest4() {
	s := []int{0, 1, 2, 3}
	p := &s[2] // *int, 获取底层数组元素指针。
	*p += 100

	fmt.Println(s) //[0 1 102 3]
}

func sliceTest5() {
	data := [][]int{
		[]int{1, 2, 3},
		[]int{100, 200},
		[]int{11, 22, 33, 44},
	}
	fmt.Println(data)
}

func sliceTest6() {
	d := [5]struct {
		x int
	}{}

	s := d[:] //指向同一个内存空间

	d[1].x = 10
	s[2].x = 20

	fmt.Printf("%p, %p, %p, %p, %p\n", &d, &d[0], s, &s, &s[0]) //0xc0000c0060, 0xc0000c0060, 0xc0000c0060, 0xc000096060, 0xc0000c0060
	fmt.Printf("d: %v,  s: %v\n", d, s)                         //d: [{0} {10} {20} {0} {0}],  s: [{0} {10} {20} {0} {0}]

	s1 := d //新建一个内存空间
	s1[3].x = 30

	fmt.Printf("%p, %p, %p, %p\n", &d, &d[0], &s1, &s1[0]) //0xc0000c0060, 0xc0000c0060, 0xc0000c00c0, 0xc0000c00c0
	fmt.Printf("d: %v,  s: %v\n", d, s1)                   //d: [{0} {10} {20} {0} {0}],  s: [{0} {10} {20} {30} {0}]

}

//slice追加添加元素，append
func sliceTest7() {
	var a = []int{1, 2, 3}
	fmt.Printf("slice a : %v\n", a)
	var b = []int{4, 5, 6}
	fmt.Printf("slice b : %v\n", b)
	c := append(a, b...)
	fmt.Printf("slice c : %v\n", c)
	d := append(c, 7)
	fmt.Printf("slice d : %v\n", d)
	e := append(d, 8, 9, 10)
	fmt.Printf("slice e : %v\n", e)
}

//虽然有多余的容量cap但是超出长度，append后
func sliceTest8() {
	s1 := make([]int, 0, 5)
	fmt.Printf("%p\n", &s1) //0xc000004078

	s1 = append(s1, 1)
	fmt.Printf("%p\n", &s1) //0xc000004078

	s2 := append(s1, 3)
	fmt.Printf("%p, %p\n", &s1, &s2) //0xc000004078, 0xc000004090
	fmt.Println(s1, s2)              //[1] [1 3]

	s2 = append(s2, 4)
	fmt.Printf("%p, %p\n", &s1, &s2) //0xc000004078, 0xc000004090

	fmt.Println(s1, s2) //[1] [1 3 4]
}

func sliceTest9() {
	data := [...]int{0, 1, 2, 3, 4, 10: 0}
	s := data[:2:3]

	s = append(s, 100) // 第一次 append 不超出 s.cap 限制。

	fmt.Println(s, data)         // 不会从新分配底层数组。  		[0 1 100] [0 1 100 3 4 0 0 0 0 0 0]
	fmt.Println(&s[0], &data[0]) // 比对底层数组起始指针。	0xc000086060 0xc000086060

	s = append(s, 200) // 一次 append 两个值，超出 s.cap 限制。

	fmt.Println(s, data)         // 重新分配底层数组，与原数组无关。  	[0 1 100 200] [0 1 100 3 4 0 0 0 0 0 0]
	fmt.Println(&s[0], &data[0]) // 比对底层数组起始指针。				0xc0000c0060 0xc000086060
}

// cap的变化
func sliceTest10() {
	s := make([]int, 0, 1)
	c := cap(s)

	for i := 0; i < 50; i++ {
		s = append(s, i)
		if n := cap(s); n > c {
			fmt.Printf("cap: %d -> %d\n", c, n)
			c = n
		}
	}
	/*
		cap: 1 -> 2
		cap: 2 -> 4
		cap: 4 -> 8
		cap: 8 -> 16
		cap: 16 -> 32
		cap: 32 -> 64
	*/
}

//copy的使用
func sliceTest11() {
	s1 := []int{1, 2, 3, 4, 5}
	fmt.Printf("slice s1 : %v\n", s1)
	s2 := make([]int, 10)
	fmt.Printf("slice s2 : %v\n", s2)
	copy(s2, s1)
	fmt.Printf("copied slice s1 : %v\n", s1)
	fmt.Printf("copied slice s2 : %v\n", s2)
	s3 := []int{1, 2, 3}
	fmt.Printf("slice s3 : %v\n", s3)
	s3 = append(s3, s2...)
	fmt.Printf("appended slice s3 : %v\n", s3)
	s3 = append(s3, 4, 5, 6)
	fmt.Printf("last slice s3 : %v\n", s3)

	/*
		slice s1 : [1 2 3 4 5]
		slice s2 : [0 0 0 0 0 0 0 0 0 0]
		copied slice s1 : [1 2 3 4 5]
		copied slice s2 : [1 2 3 4 5 0 0 0 0 0]
		slice s3 : [1 2 3]
		appended slice s3 : [1 2 3 1 2 3 4 5 0 0 0 0 0]
		last slice s3 : [1 2 3 1 2 3 4 5 0 0 0 0 0 4 5 6]
	*/

}

//copy的使用
func sliceTest12() {
	data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	fmt.Println("array data : ", data)
	s1 := data[8:]
	s2 := data[:5]
	fmt.Printf("slice s1 : %v\n", s1)
	fmt.Printf("slice s2 : %v\n", s2)
	copy(s2, s1)
	fmt.Printf("copied slice s1 : %v\n", s1)
	fmt.Printf("copied slice s2 : %v\n", s2)
	fmt.Println("last array data : ", data)

	/*
		array data :  [0 1 2 3 4 5 6 7 8 9]
		slice s1 : [8 9]
		slice s2 : [0 1 2 3 4]
		copied slice s1 : [8 9]
		copied slice s2 : [8 9 2 3 4]
		last array data :  [8 9 2 3 4 5 6 7 8 9]
	*/
}

//使用 for range 遍历
func sliceTest13() {
	data := [...]int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	slice := data[:]
	for index, value := range slice {
		fmt.Printf("index : %v , value : %v\n", index, value)
	}
	/*
		index : 0 , value : 0
		index : 1 , value : 1
		index : 2 , value : 2
		index : 3 , value : 3
		index : 4 , value : 4
		index : 5 , value : 5
		index : 6 , value : 6
		index : 7 , value : 7
		index : 8 , value : 8
		index : 9 , value : 9
	*/
}

//slice 截取赋值的时候注意事项 重点看 c
func sliceTest14() {
	var a = []int{1, 3, 4, 5}
	fmt.Printf("slice a : %v , len(a) : %v\n", a, len(a)) //slice a : [1 3 4 5] , len(a) : 4

	b := a[1:2]
	fmt.Printf("slice b : %v , len(b) : %v\n", b, len(b)) //slice b : [3] , len(b) : 1

	c := b[0:3]
	fmt.Printf("slice c : %v , len(c) : %v\n", c, len(c)) //slice c : [3 4 5] , len(c) : 3
}

//slice string的操作
func sliceTest15() {
	str := "hello world"
	s1 := str[0:5]
	fmt.Println(s1) //hello

	s2 := str[6:]
	fmt.Println(s2) //world
}

//slice string的操作
func sliceTest16() {
	str := "Hello world"
	s := []byte(str) //中文字符需要用[]rune(str)
	s[6] = 'G'
	s = s[:8]
	s = append(s, '!')
	str = string(s)
	fmt.Println(str) //Hello Go!
}

//slice string的操作
func sliceTest17() {
	str := "你好，世界！hello world！"
	s := []rune(str)
	s[3] = '够'
	s[4] = '浪'
	s[12] = 'g'
	s = s[:14]
	str = string(s)
	fmt.Println(str) //你好，够浪！hello go
}

//[:] 截取的时候cap是 原有数据的截取后的容量
func sliceTest18() {
	slice := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	d1 := slice[5:7]
	fmt.Println(d1, len(d1), cap(d1)) //[5 6] 2 5
	d2 := slice[:6:8]
	fmt.Println(d2, len(d2), cap(d2)) //[0 1 2 3 4 5] 6 8
}

func sliceTest19() {
	slice := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	str := strings.Replace(strings.Trim(fmt.Sprint(slice), "[]"), " ", ",", -1)
	fmt.Println(str) //0,1,2,3,4,5,6,7,8,9
}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`