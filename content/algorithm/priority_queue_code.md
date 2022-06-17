---
author: "li_mingxie"
title: "【算法笔记】PriorityQueue的简单实践"
date: 2022-02-15T07:28:49+08:00
tags: [
    "Priority Queue",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

今天用go语言简单的写了一下PriorityQueue的方法。  
为了以后方便查看，当做笔记整理了一下~~  
<!--more-->

## 1.优先队列(PriorityQueue)

[维基百科](https://zh.wikipedia.org/wiki/%E5%84%AA%E5%85%88%E4%BD%87%E5%88%97)里是这么解释的。

> 优先队列是计算机科学中的一类抽象数据类型。优先队列中的每个元素都有各自的优先级，优先级最高的元素最先得到服务；  
> 优先级相同的元素按照其在优先队列中的顺序得到服务。优先队列往往用堆来实现。  

下面用go语言简单的实现了PriorityQueue。

#### struct

```go
type PriorityQueue struct {
 Head *Node
}

type Node struct {
 Value    string
 Priority int
 Next     *Node
}
```

#### Push

```go
func (p *PriorityQueue) Push(value string, priority int) {
 if p.Head == nil {
  p.Head = &Node{Value: value, Priority: priority}
  return
 }
 newNode := &Node{Value: value, Priority: priority}
 currentNode := p.Head
 for currentNode.Next != nil && currentNode.Next.Priority > priority {
  currentNode = currentNode.Next
 }
 newNode.Next = currentNode.Next
 currentNode.Next = newNode
}
```

#### Pop

```go
func (p *PriorityQueue) Pop() *Node {
 if p.Head == nil {
  return nil
 }
 result := p.Head
 p.Head = p.Head.Next
 return result
}
```

#### Peek,IsEmpty,Print

```go
func (p *PriorityQueue) Peek() *Node {
 return p.Head
}

func (p *PriorityQueue) IsEmpty() bool {
 return p.Head == nil
}

func (n *Node) Print() {
 if n == nil {
  return
 }
 fmt.Print(n.Value, " ")
 n.Next.Print()
}
```

#### 执行结果

```go
func main() {
    priorityQueue := PriorityQueue{}
 priorityQueue.Push("a", 100)
 priorityQueue.Push("b", 83)
 priorityQueue.Push("c", 64)
 priorityQueue.Push("e", 37)
 priorityQueue.Push("f", 23)

 fmt.Println("-------- Print  --------")
 priorityQueue.Head.Print()
 fmt.Println("")

 fmt.Println("-------- Pop  --------")
 fmt.Printf("%+v", priorityQueue.Pop())
 fmt.Println("")
 priorityQueue.Head.Print()
 fmt.Println("")

 fmt.Println("-------- Push z --------")
 priorityQueue.Push("z", 53)
 priorityQueue.Head.Print()
 fmt.Println("")

 fmt.Println("-------- Peek()  --------")
 fmt.Printf("%+v", priorityQueue.Peek()) 
}
```

执行结果为：

```
$ go run main.go
-------- Print  --------
a b c e f 
-------- Pop  --------
&{Value:a Priority:100 Next:0xc0000a43c0}
b c e f
-------- Push z --------
b c z e f
-------- Peek()  --------
&{Value:b Priority:83 Next:0xc0000a43e0}
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
