---
author: "li_mingxie"
title: "【算法笔记】数组(Array)的模拟实践"
date: 2022-01-25T07:28:49+08:00
tags: [
    "Array",
    "List",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

今天用go语言简单的写了一下数组的方法。  
为了以后方便查看，当做笔记整理了一下~~  


## 1.数组(Array)

[维基百科](https://zh.wikipedia.org/wiki/%E6%95%B0%E7%BB%84)里是这么解释的。

> 简称数组（英语：Array），是由相同类型的元素（element）的集合所组成的数据结构，分配一块连续的内存来存储。  
> 利用元素的索引（index）可以计算出该元素对应的存储地址。

本想写个通用的方法，但是写着写着感觉需要处理的细节太多了，  
本人不才只能简单的写了一下[]int 的数组模式...^^;;

#### struct

```go
type ArrayList struct {
	Data []int
	Size int
}
```

#### 查询相关方法

```go
func (arrayList *ArrayList) FindIndex(value int) int {
	if arrayList == nil || arrayList.Data == nil {
		return -1
	}
	for i, v := range arrayList.Data {
		if v == value {
			return i
		}
	}
	return -1
}

func (arrayList *ArrayList) Contains(value int) bool {
	if arrayList == nil || arrayList.Data == nil {
		return false
	}
	for i := range arrayList.Data {
		if value == arrayList.Data[i] {
			return true
		}
	}
	return false
}

func (arrayList *ArrayList) GetLength() int {
	return arrayList.Size
}

func (arrayList *ArrayList) GetCapacity() int {
	return cap(arrayList.Data)
}

func (arrayList *ArrayList) IsEmpty() bool {
	return arrayList.Size == 0
}
```

#### Add相关方法

```go
func (arrayList *ArrayList) Add(index int, value int) error {
	if arrayList == nil || arrayList.Size == 0 {
		if index != 0 {
			return errors.New("add faild. arrayList is empty. index must be 0")
		} else {
			if arrayList.Data == nil {
				arrayList.Data = []int{value}
				arrayList.Size++
			} else {
				arrayList.Data = append(arrayList.Data, value)
				arrayList.Size++

			}

		}
		return nil
	}
	if index < 0 {
		return errors.New("add faild. index out of range")
	}

    //判断数组是否有剩余空间，先把值放到最后
	if arrayList.Size < len(arrayList.Data) {
		arrayList.Data[arrayList.Size] = value
	} else {
		arrayList.Data = append(arrayList.Data, value)
	}
	arrayList.Size++

    //放到最后的值挪动的相对应的index的位置
	var tempValue int = 0
	for i := arrayList.Size - 1; i > index; i-- {
		tempValue = arrayList.Data[i]
		arrayList.Data[i] = arrayList.Data[i-1]
		arrayList.Data[i-1] = tempValue
	}

	return nil
}

func (arrayList *ArrayList) AddFirst(value int) error {
	return arrayList.Add(0, value)
}

func (arrayList *ArrayList) AddLast(value int) error {
	return arrayList.Add(arrayList.Size, value)
}
```

#### Remove相关方法

```go
func (arrayList *ArrayList) Remove(index int) (bool, error) {
	if index < 0 || index > arrayList.Size-1 {
		return false, errors.New("remove faild. index out of range")
	}

    //因为是删除，所以直接把对应index后的值都向前挪一个位置
	for i := index; i < arrayList.Size-1; i++ {
		arrayList.Data[i] = arrayList.Data[i+1]
	}
	arrayList.Data[arrayList.Size-1] = 0
	arrayList.Size--

    //为了防止占用太多的空间，需及时回收剩余空间
	if arrayList.Size < len(arrayList.Data)/2 {
		arrayList.ReSetSize()
	}

	return true, nil
}

func (arrayList *ArrayList) ReSetSize() {
    //根据需求这里可以分配更多，或不分配剩余空间
	values := make([]int, arrayList.Size*3/2)
	for i := range arrayList.Data {
		if arrayList.Data[i] != 0 {
			values[i] = arrayList.Data[i]
		}
	}
	arrayList.Data = values
}

func (arrayList *ArrayList) RemoveFirst() (bool, error) {
	return arrayList.Remove(0)
}

func (arrayList *ArrayList) RemoveLast() (bool, error) {
	return arrayList.Remove(arrayList.Size - 1)
}

func (arrayList *ArrayList) RemoveValue(value int) (bool, error) {
	index := arrayList.Find(value)
	if index != -1 {
		return arrayList.Remove(index)
	} else {
		return false, errors.New("remove faild. no values to remove")
	}
}
```

删除时还有ReSetSize的方法，是为了删除后把剩余空间腾出来。  
这里是从新分出 3/2的空间是因为免得下次添加时再一次的分配空间。  
可以按照实际情况分的更多，也可以部分剩余空间。  


#### ToString

```go
func (arrayList *ArrayList) ToString() string {
	return fmt.Sprint(arrayList)
}
```

#### 执行结果

```go
func main() {
	var arrayList ArrayList
	for i := 0; i < 10; i++ {
		arrayList.Add(i, i+1)
	}
	fmt.Println("--------------Add---------------")
	fmt.Println(arrayList)

	fmt.Println("--------------Remove(4)---------------")
	arrayList.Remove(4)
	fmt.Println(arrayList)

	fmt.Println("--------------Add(4,5)---------------")
	arrayList.Add(4, 5)
	fmt.Println(arrayList)

	fmt.Println("--------------ReSetSize---------------")
	for i := 0; i < 8; i++ {
		arrayList.RemoveLast()
		fmt.Printf("len: %d, cap:%d, Values:%+v, Size: %d \n", len(arrayList.Data), cap(arrayList.Data), arrayList.Data, arrayList.Size)
	}
}
```

执行结果为：
```
$ go run main.go
--------------Add---------------
{[1 2 3 4 5 6 7 8 9 10] 10}
--------------Remove(4)---------------
{[1 2 3 4 6 7 8 9 10 0] 9}
--------------Add(4)---------------
{[1 2 3 4 5 6 7 8 9 10] 10}
--------------ReSetSize---------------
len: 10, cap:16, Values:[1 2 3 4 5 6 7 8 9 0], Size: 9
len: 10, cap:16, Values:[1 2 3 4 5 6 7 8 0 0], Size: 8
len: 10, cap:16, Values:[1 2 3 4 5 6 7 0 0 0], Size: 7
len: 10, cap:16, Values:[1 2 3 4 5 6 0 0 0 0], Size: 6
len: 10, cap:16, Values:[1 2 3 4 5 0 0 0 0 0], Size: 5
len: 6, cap:6, Values:[1 2 3 4 0 0], Size: 4
len: 6, cap:6, Values:[1 2 3 0 0 0], Size: 3
len: 3, cap:3, Values:[1 2 0], Size: 2
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`