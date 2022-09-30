---
author: "li_mingxie"
title: "【算法笔记】链表(Linked List)的简单实践"
date: 2022-01-29T07:28:49+08:00
tags: [
    "Linked List",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

今天用go语言简单的写了一下单向链表的方法。  
为了以后方便查看，当做笔记整理了一下~~  
<!--more-->

## 1.链表(Linked List)

[维基百科](https://zh.wikipedia.org/wiki/%E9%93%BE%E8%A1%A8)里是这么解释的。

> 链表（Linked list）是一种常见的基础数据结构，是一种线性表，  
> 但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的指针(Pointer)。  
> 由于不必须按顺序存储，链表在插入的时候可以达到O(1)的复杂度，比另一种线性表顺序表快得多，  
> 但是查找一个节点或者访问特定编号的节点则需要O(n)的时间，而顺序表相应的时间复杂度分别是O(logn)和O(1)。

看看下图:  

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_1.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_1.png)

简单的说就是**把数据链接起来保存的数据结构。**  
因为需要链接，所以每个节点都需要存储下一个节点的信息。  
这一特性是链表的插入和删除比较快。查询慢(因为只记录下一个节点，想找特定的值只能全部遍历)。  
但是数组就恰恰相反，查询很快，插入和删除的效率就不高了。  
可以看看下图：  

#### 链表的插入

只要把3和7的中间加入5就行了。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_11.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_11.png)

#### 链表的删除

这就更简单了，直接把3连接到7就行了。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_1.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_12.png)

#### 数组的插入

先把7和9各向右挪一格，然后把5放进去。如果是个很长的数组，这效率就可想而知了。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_21.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_21.png)

#### 数组的删除

这也是先吧5删除后，再把7和9挪到左边。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_31.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_31.png)

##### 这里简单的说明一下，二叉树就是有链表的插入和删除的高效率，而且又有不慢的查询速度的数组和链表相结合的数据结构

## 2.链表的结构

链表有好几种结构，但是比较常有的有3种。  
单向链表，双向链表，循环链表。

#### 单向链表

链表中最简单的一种是单向链表，它包含两个域，一个信息域和一个指针域。这个链接指向列表中的下一个节点，而最后一个节点则指向一个空值。
看下图应该很容易理解。  

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_2.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_2.png)

#### 双向链表

每个节点有两个连接：一个指向前一个节点，而另一个指向下一个节点。  

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_3.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_3.png)

#### 循环链表

首节点和末节点被连接在一起。形成一个循环。

[图片备用地址](https://limingxie.github.io/images/algorithm/base/linked_list_4.png)  
![linked_list](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/base/linked_list_4.png)

----------------------------------------------

## 3.单向链表的实例

#### struct

```go
//链表
type LinkedList struct {
 Head *ListNode  //头部节点
 Tail *ListNode  //尾部节点
 Size int        //链表长度
}

//节点
type ListNode struct {
 Value int   //节点的值
 Next  *ListNode //下一个节点
}
```

#### Insert

```go
//position: 要插入的位置
//number: 要插入的值
func (node *LinkedList) Insert(position int, number int) {
 if position > node.Size {
  return
 }

 newNode := &ListNode{Value: number}

 if position == 0 {
  newNode.Next = node.Head
  node.Head = newNode
  if node.Tail == nil {
   node.Tail = newNode
  }
 } else if position == node.Size {
  node.Tail.Next = newNode
  node.Tail = newNode
 } else {
  currentNode := node.Head
  for i := 0; i < position; i++ {
   currentNode = currentNode.Next
  }

  newNode.Next = currentNode.Next
  currentNode.Next = newNode
 }
 node.Size++
}
```

#### Delete

```go
//number: 需要删除的值
func (node *LinkedList) Delete(number int) {
 if node.Head == nil || node.Tail == nil {
  return
 }

 if node.Head.Value == number {
  node.Head = node.Head.Next
  node.Size--
  if node.Size == 0 {
   node.Tail = node.Head
  }
  return
 }

 preNode := node.Head
 curNode := node.Head
 for i := 0; i < node.Size; i++ {
  if curNode.Value == number {
   if curNode == node.Tail {
    node.Tail = preNode
   }
   preNode.Next = curNode.Next
   node.Size--
   return
  }
  preNode = curNode
  curNode = curNode.Next
 }
}
```

#### Update

```go
func (node *LinkedList) Update(old_number int, new_number int) int {
 currentNode := node.Head
 for i := 0; i < node.Size; i++ {
  if currentNode.Value == old_number {
   currentNode.Value = new_number
   return i
  }
  currentNode = currentNode.Next
 }
 return -1
}
```

#### Search

```go
func (node *LinkedList) Search(number int) int {
 currentNode := node.Head
 for i := 0; i < node.Size; i++ {
  if currentNode.Value == number {
   return i
  }
  currentNode = currentNode.Next
 }
 return -1
}
```

#### Print

```go
func (node *ListNode) Print() {
 if node == nil || node.Value == 0 {
  return
 } else {
  fmt.Print(node.Value, " ")
  node.Next.Print()
 }
}
```

#### 执行结果

```go
func main() {
 var linkedlist LinkedList
 linkedlist.Insert(0, 1)
 linkedlist.Insert(1, 2)
 linkedlist.Insert(2, 3)
 linkedlist.Insert(3, 4)
 linkedlist.Insert(4, 5)
 linkedlist.Insert(5, 6)
 linkedlist.Insert(6, 7)
 linkedlist.Insert(7, 8)
 fmt.Println("----insert 1,2,3,4,5,6,7,8----")
 linkedlist.Head.Print()
 fmt.Println("")
 fmt.Println("------update 5 => 55 ------")
 linkedlist.Update(5, 55)
 linkedlist.Head.Print()
 fmt.Println("")
 fmt.Println("-------- delete 55 --------")
 linkedlist.Delete(55)
 linkedlist.Head.Print()
 fmt.Println("")
 fmt.Println("-------- delete 9 --------")
 linkedlist.Delete(9)
 linkedlist.Head.Print()
 fmt.Println("")
 fmt.Println("-------- search 4  --------")
 fmt.Println("index:", linkedlist.Search(4))
}
```

执行结果为：

```
$ go run main.go
----insert 1,2,3,4,5,6,7,8----
1 2 3 4 5 6 7 8 
------update 5 => 55 ------
1 2 3 4 55 6 7 8
-------- delete 55 --------
1 2 3 4 6 7 8
-------- delete 9 --------
1 2 3 4 6 7 8
-------- search 4  --------
index: 3
```

----------------------------------------------

今天我看资料有个面试题是反转单项链表，我以为会很简单，结果写了半小时...^^;;
有几个坑大家需要注意一下...

#### struct

```go
//链表
type LinkedList struct {
 Head *ListNode  //头部节点
 Tail *ListNode  //尾部节点
 Size int        //链表长度
}

//节点
type ListNode struct {
 Value int   //节点的值
 Next  *ListNode //下一个节点
}
```

#### 反转链表方法

我们先看看错误的写法

```go
func (l *LinkList) ReverseLinkedList() {
 if l == nil || l.Head == nil {
  return
 }
 node := Node{Value: l.Head.Value}
 curNode := l.Head
 for curNode.Next != nil {
  newNode := Node{Value: curNode.Value}
  newNode.Next = &node
  node = newNode
  curNode = curNode.Next
 }
 l.Head = &node
}
```

再看看正确的写法

```go
func (l *LinkList) ReverseLinkedList() {
 if l == nil || l.Head == nil {
  return
 }
 node := Node{}
 curNode := l.Head
 for curNode != nil {
  newNode := node 
  node.Value = curNode.Value
  node.Next = &newNode
  curNode = curNode.Next
 }
 l.Head = &node
}
```

原因其实很简单，我们习惯性的赋值只会修改指针指向的地方，后续的操作就可想而知了。  

#### 执行结果

```go
func (node *Node) Print() {
 if node == nil || node.Value == 0 {
  return
 } else {
  fmt.Print(node.Value, " ")
  node.Next.Print()
 }
}

func main() {
 linkList := LinkList{}
 linkList.Head = &Node{Value: 1}
 linkList.Head.Next = &Node{Value: 2}
 linkList.Head.Next.Next = &Node{Value: 3}
 linkList.Head.Next.Next.Next = &Node{Value: 4}

 linkList.ReverseLinkedList()  

 linkList.Head.Print()
}
```

执行结果为：

```
$ go run main.go
4 3 2 1 
```

----------------------------------------------

## 约瑟夫问题 1

#### struct

```go
type CircleLinkedList struct {
 Head *ListNode
 Tail *ListNode
 Size int
}
```

#### 方法

```go
func (c *CircleLinkedList) JosephusCircleLinkedList(startNo, count, sum int) {
 for i := 1; i < startNo; i++ {
  c.Head = c.Head.Next
  c.Tail = c.Tail.Next
 }

 for {
  if c.Head == c.Tail {
   break
  }
  for i := 0; i < count-1; i++ {
   c.Head = c.Head.Next
   c.Tail = c.Tail.Next
  }
  fmt.Printf("出队元素：%d\n", c.Head.Value)
  c.Head = c.Head.Next
  c.Tail.Next = c.Head

 }
 fmt.Printf("最后剩下的元素是：%d", c.Head.Value)
}
```

#### 执行结果

```go
func main() {
 c := CircleLinkedList{}
 c.Head = &ListNode{Value: 1}
 c.Head.Next = &ListNode{Value: 2}
 c.Head.Next.Next = &ListNode{Value: 3}
 c.Head.Next.Next.Next = &ListNode{Value: 4}
 c.Head.Next.Next.Next.Next = &ListNode{Value: 5}
 c.Tail = c.Head.Next.Next.Next.Next
 c.Tail.Next = c.Head
 c.Size = 5
 c.JosephusCircleLinkedList(1, 2, 5)
}
```

执行结果为：

```
$ go run main.go
出队元素：3
出队元素：5
出队元素：2
出队元素：1
最后剩下的元素是：4
```

## 约瑟夫问题 2

不同的唯独解答

```go
package main

import (
 "fmt"
)

type CircleLinkedList struct {
 Head *Node
 Tail *Node
 Size int
}

type Node struct {
 Value    int
 NextNode *Node
}

func (c *CircleLinkedList) JosephusCircleLinkedList(startNo, count int) {
 node := c.Head
 endNode := c.Head
 for i := 1; i < startNo; i++ {
  node = node.NextNode
 }
 for i := 1; i <= (startNo+c.Size-2)%c.Size; i++ {
  endNode = endNode.NextNode
 }
 fmt.Println("-----------当前node和尾随node------------")
 fmt.Printf("%p => %v \n", node, node)
 fmt.Printf("%p => %v \n", endNode, endNode)
 for {
  for i := 1; i <= count; i++ {
   node = node.NextNode
   endNode = endNode.NextNode
  }

  endNode.NextNode = node.NextNode
  fmt.Printf("出列 %v\n", node.Value)
  node = node.NextNode
  if node == endNode {
   fmt.Printf("出列 %v\n", node.Value)
   break
  }
 }
}

func CreateCircleLinkedList(count int) *CircleLinkedList {
 c := &CircleLinkedList{Size: count}
 node := &Node{Value: 1}
 c.Head = node

 for i := 1; i < count; i++ {
  n := &Node{Value: i + 1}
  node.NextNode = n
  node = n
 }
 node.NextNode = c.Head
 return c
}

func (c *CircleLinkedList) Print() {
 node := c.Head
 for i := 0; i < c.Size; i++ {
  fmt.Printf("%p => %v \n", node, node)
  node = node.NextNode
 }
}

func main() {
 c := CreateCircleLinkedList(9)
 c.Print()

 c.JosephusCircleLinkedList(2, 2)
}
```

**执行结果**  

```bash
$ go run main.go
0xc000096210 => &{1 0xc000096220} 
0xc000096220 => &{2 0xc000096230} 
0xc000096230 => &{3 0xc000096240} 
0xc000096240 => &{4 0xc000096250} 
0xc000096250 => &{5 0xc000096260} 
0xc000096260 => &{6 0xc000096270} 
0xc000096270 => &{7 0xc000096280} 
0xc000096280 => &{8 0xc000096290} 
0xc000096290 => &{9 0xc000096210} 
-----------当前node和尾随node------------
0xc000096220 => &{2 0xc000096230} 
0xc000096210 => &{1 0xc000096220} 
出列 4
出列 7
出列 1
出列 5
出列 9
出列 6
出列 3
出列 8
出列 2
```

----------------------------------------------

## 其它

好久没接触算法题了，写写看看，记过折腾了快1个多小时...^^;;  

```go
package main

import (
 "fmt"
)

type MainNode struct {
 Front *Node
 Tail  *Node
 Size  int
}

type Node struct {
 No       int
 Value    string
 PreNode  *Node
 NextNode *Node
}

func (m *MainNode) Push(no int, val string) {
 node := &Node{
  No:    no,
  Value: val,
 }
 if m.Size == 0 {
  node.PreNode = nil
  node.NextNode = nil
  m.Front = node
  m.Tail = node
 } else if m.Size == 1 {
  temp := m.Front
  if temp.No > node.No {
   node.PreNode = nil
   node.NextNode = temp
   temp.PreNode = node
   m.Front = node
   m.Tail = temp
  } else {
   temp.NextNode = node
   node.PreNode = temp
   m.Tail = node
  }
 } else {
  temp := m.Front
  isEdit := false
  for i := 0; i < m.Size; i++ {
   if no < temp.No {
    temp.PreNode.NextNode = node
    node.PreNode = temp.PreNode
    temp.PreNode = node
    node.NextNode = temp
    isEdit = true
    break
   }
   temp = temp.NextNode
  }
  if !isEdit {
   node.PreNode = m.Tail
   m.Tail.NextNode = node
   m.Tail = node

  }
 }
 m.Size++
}

func (m *MainNode) Peek() *Node {
 if m.Size == 0 {
  fmt.Println("队列已空，无法Peek")
 }
 result := m.Front

 if result.NextNode == nil {
  m.Front = nil
  m.Tail = nil
 } else {
  m.Front = result.NextNode
  m.Front.PreNode = nil
 }

 m.Size--

 return result

}

func (m *MainNode) Pop() *Node {
 if m.Size == 0 {
  fmt.Println("队列已空，无法Pop")
 }

 result := m.Tail

 if result.PreNode == nil {
  m.Front = nil
  m.Tail = nil
 } else {
  m.Tail = result.PreNode
  m.Tail.NextNode = nil
 }
 m.Size--

 return result

}

func (m *MainNode) Get(v string) *Node {
 if m.Size == 0 {
  fmt.Println("队列已空，无法Get")
 }
 temp := m.Front
 for i := 0; i < m.Size; i++ {
  if temp.Value == v {
   return temp
  }
  temp = temp.NextNode
 }
 return nil
}

func (m *MainNode) Remove(v string) {
 if m.Size == 0 {
  fmt.Println("队列已空，无法Remove")
 }

 temp := m.Front
 isRemoved := false
 for i := 0; i < m.Size; i++ {
  if temp.Value == v {
   if temp.PreNode == nil {
    temp.NextNode.PreNode = nil
    m.Front = temp.NextNode
   } else {
    temp.PreNode.NextNode = temp.NextNode
   }
   m.Size--
   isRemoved = true
   break
  }
  temp = temp.NextNode
 }
 if isRemoved {
  fmt.Println(v, "删除成功")
 } else {
  fmt.Println("队列中没有对应的数据")
 }
}

func (n *Node) Print() {
 if n != nil {
  fmt.Printf("no: %v, val: %v\n", n.No, n.Value)
  if n.NextNode != nil {
   n.NextNode.Print()
  }
 }
}

func main() {
 fmt.Println("------------Push-------------")
 mainNode := &MainNode{}
 mainNode.Push(1, "小明")
 mainNode.Push(3, "小张")
 mainNode.Push(5, "小李")
 mainNode.Push(2, "小红")
 mainNode.Push(4, "张三")
 fmt.Println(mainNode.Size)
 mainNode.Front.Print()

 fmt.Println("------------Peek-------------")
 fmt.Println(mainNode.Peek())
 fmt.Println(mainNode.Peek())

 fmt.Println(mainNode.Size)
 mainNode.Front.Print()

 fmt.Println("------------Pop--------------")
 fmt.Println(mainNode.Pop())
 fmt.Println(mainNode.Pop())

 fmt.Println(mainNode.Size)
 fmt.Printf("%v\n", mainNode)
 mainNode.Front.Print()

 fmt.Println("----------Get,Remove---------")
 mainNode.Push(1, "小明")
 // mainNode.Push(3, "小张")
 mainNode.Push(5, "小李")
 mainNode.Push(2, "小红")
 mainNode.Push(4, "张三")

 fmt.Println(mainNode.Size)
 fmt.Printf("%v\n", mainNode)
 mainNode.Front.Print()

 fmt.Println(mainNode.Get("小红"))

 mainNode.Remove("小红")

 fmt.Println(mainNode.Size)
 fmt.Printf("%v\n", mainNode)
 mainNode.Front.Print()
}
```

**执行结果**  

```bash
$ go run main.go
------------Push-------------
5
no: 1, val: 小明
no: 2, val: 小红
no: 3, val: 小张
no: 4, val: 张三
no: 5, val: 小李
------------Peek-------------
&{1 小明 <nil> 0xc00018e0c0}
&{2 小红 <nil> 0xc00018e060}
3
no: 3, val: 小张
no: 4, val: 张三
no: 5, val: 小李
------------Pop--------------
&{5 小李 0xc00018e0f0 <nil>}
&{4 张三 0xc00018e060 <nil>}
1
&{0xc00018e060 0xc00018e060 1}
no: 3, val: 小张
----------Get,Remove---------
5
&{0xc00018e1e0 0xc00018e210 5}
no: 1, val: 小明
no: 2, val: 小红
no: 3, val: 小张
no: 4, val: 张三
no: 5, val: 小李
&{2 小红 0xc00018e1e0 0xc00018e060}
小红 删除成功
4
&{0xc00018e1e0 0xc00018e210 4}
no: 1, val: 小明
no: 3, val: 小张
no: 4, val: 张三
no: 5, val: 小李
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
