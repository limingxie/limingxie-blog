---
author: "li_mingxie"
title: "【算法笔记】queue的简单实践"
date: 2022-02-14T07:28:49+08:00
tags: [
    "queue",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

今天用go语言简单的写了一下queue的方法。  
为了以后方便查看，当做笔记整理了一下~~  
<!--more-->

## 1.队列(QUEUE)

[维基百科](https://zh.wikipedia.org/wiki/%E5%A0%86%E6%A0%88)里是这么解释的。

> 计算机科学中的一种抽象资料型别，是先进先出（FIFO, First-In-First-Out）的线性表。  
> 在具体应用中通常用链表或者数组来实现。队列只允许在后端（称为rear）进行插入操作，在前端（称为front）进行删除操作。  
> 队列的操作方式和堆栈类似，唯一的区别在于队列只允许新数据在后端进行添加。  

具体的详解可以参考这篇文章里的Queue部分[栈(STACK), 堆(HEAP), 队列(QUEUE) 是什么？](https://limingxie.github.io/basic/stack/)

## 2.数组队列

#### struct

```go
type Queue struct {
 Front    int
 Rear     int
 Size     int
 Capacity int
 Values   []int
}
```

#### 入队(Enqueue)

```go
func (q *Queue) Enqueue(item int) {
 if q.IsFull() {
  fmt.Println("queue is full!")
  return
 }

 q.Values[q.Rear] = item
 q.Rear = (q.Rear + 1) % q.Capacity
 q.Size++
}
```

#### 这里须注明一下

1. Enqueue方法下面，可以写扩充队列的逻辑：比如队列快满了，Size/Capacity>80% 就扩充队列空间。  
2. 下面Dequeue的时候也是 如果Size/Capacity < 40%, 可以写缩减空间的逻辑。  
3. (q.Rear + 1) % q.Capacity 部分是为了循环使用空间，使用了循环队列。  
如下图：

[图片备用地址](https://limingxie.github.io/images/basic/queue_2.png)  
![queue](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/basic/queue_2.png?x-oss-process=image/resize,w_300,m_lfit)

#### 出队(Dequeue)

```go
func (q *Queue) Dequeue() int {
 if q.IsEmpty() {
  fmt.Println("queue is empty!")
  return -1
 }
 front := q.Values[q.Front]
 q.Front = (q.Front + 1) % q.Capacity
 return front
}
```

#### Peek, IsFull, IsEmpty

```go
func (q *Queue) Peek() int {
 if q.IsEmpty() {
  fmt.Println("queue is empty!")
  return -1
 }

 return q.Values[q.Front]
}

func (q *Queue) IsFull() bool {
 return q.Size == q.Capacity
}

func (q *Queue) IsEmpty() bool {
 return q.Size == 0
}
```

#### 执行结果

```go
func main() {
 queue := Queue{Capacity: 10}
 queue.Values = make([]int, queue.Capacity)
 queue.Enqueue(11)
 queue.Enqueue(12)
 queue.Enqueue(13)
 fmt.Println("-------- queue.Values  --------")
 fmt.Printf("%+v", queue.Values)
 fmt.Println("")
 fmt.Println("-------- Front, Rear  --------")
 fmt.Printf("Front: %v, Rear: %v", queue.Front, queue.Rear)
 fmt.Println("")
 queue.Enqueue(14)
 fmt.Println("-------- queue.Values  --------")
 fmt.Printf("%+v", queue.Values)
 fmt.Println("")
 fmt.Println("-------- Front, Rear  --------")
 fmt.Printf("Front: %v, Rear: %v", queue.Front, queue.Rear)
 fmt.Println("")
 fmt.Println("-------- queue.IsEmpty  --------")
 fmt.Println(queue.IsEmpty())
 fmt.Println("-------- queue.IsFull  --------")
 fmt.Println(queue.IsFull())
 fmt.Println("-------- queue.Dequeue  --------")
 fmt.Println(queue.Dequeue())
 fmt.Println("-------- Front, Rear  --------")
 fmt.Printf("Front: %v, Rear: %v", queue.Front, queue.Rear)
}
```

执行结果为：

```
$ go run main.go
-------- queue.Values  --------
[11 12 13 0 0 0 0 0 0 0]
-------- Front, Rear  --------
Front: 0, Rear: 3
-------- queue.Values  --------
[11 12 13 14 0 0 0 0 0 0]
-------- Front, Rear  --------
Front: 0, Rear: 4
-------- queue.IsEmpty  --------
false
-------- queue.IsFull  --------
false
-------- queue.Dequeue  --------
11
-------- Front, Rear  --------
Front: 1, Rear: 4
```

## 2. 链式队列

#### struct

```go
type ListQueue struct {
 Front *QueueNode
 Rear  *QueueNode
}

type QueueNode struct {
 Value int
 Next  *QueueNode
}
```

#### 入队(Enqueue)

```go
func (q *ListQueue) Enqueue(val int) {
 newNode := QueueNode{Value: val}
 if q.Rear == nil {
  q.Front = &newNode
  q.Rear = &newNode
  return
 }

 q.Rear.Next = &newNode
 q.Rear = &newNode
}
```

#### 出队(Dequeue)

```go
func (q *ListQueue) Dequeue() int {
 if q.Front == nil {
  fmt.Println("queue is empty!")
  return -1
 }
 front := q.Front
 q.Front = q.Front.Next
 if q.Front == nil {
  q.Rear = nil
 }

 return front.Value
}
```

#### Print

```go
func (node *QueueNode) Print() {
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
 listQueue := ListQueue{}
 listQueue.Enqueue(11)
 listQueue.Enqueue(12)
 listQueue.Enqueue(13)
 listQueue.Enqueue(14)

 fmt.Println("-------- listQueue.Values  --------")
 listQueue.Front.Print()
 fmt.Println("")

 fmt.Println("-------- listQueue.Dequeue  --------")
 fmt.Println(listQueue.Dequeue())

 fmt.Println("-------- listQueue.Values  --------")
 listQueue.Front.Print()
}
```

执行结果为：

```
$ go run main.go
-------- listQueue.Values  --------
11 12 13 14 
-------- listQueue.Dequeue  --------
11
-------- listQueue.Values  --------
12 13 14
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
