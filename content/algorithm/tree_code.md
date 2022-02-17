---
author: "li_mingxie"
title: "【算法笔记】排序二叉树(Binary Search Tree)的简单代码实现"
date: 2022-02-16T07:28:49+08:00
tags: [
    "tree",
    "算法",
    "algorithm",
]
categories: [
    "algorithm"
]
---

简单的写了一下排序二叉树(Binary Search Treee)的查询，删除。  
加上树的遍历前序遍历，中序遍历，后序遍历。

#### 1.排序二叉树(Binary Search Tree)的结构

先声明一个BSTTree和TreeNode两个struct。

```go
type BSTTree struct {
	Root *TreeNode
}

type TreeNode struct {
	Value int
	Left  *TreeNode
	Right *TreeNode
}
```

#### 2.排序二叉树(Binary Search Tree)的查询

1. 从根(root)节点开始查看。值比根节点小的话查询左子树，大的话查询右子树。  
2. 到下一个节点以后，还重复以上操作，直到找到值或到最底层跳出二叉树。  

详细的操作方式可以参考下图：

[图片备用地址](https://limingxie.github.io/images/algorithm/tree/bst_tree_search.png)  
![bst_tree_search](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/tree/bst_tree_search.png)

```go
func (b *BSTTree) Get(val int) *TreeNode {
	currentNode := b.Root
	for currentNode != nil && currentNode.Value != val {
		if currentNode.Value == val {
			break
		} else if currentNode.Value > val {
			currentNode = currentNode.Left
		} else {
			currentNode = currentNode.Right
		}
	}
	return currentNode
}
```

#### 3.排序二叉树(Binary Search Tree)的插入

1. 从根(root)节点开始查看。值比根节点小的话查询左子树，大的话查询右子树。  
2. 到下一个节点以后，重复以上操作，直到找到节点的子节点为空的地方添加一个叶节点(Leaf)。  

详细的操作方式可以参考下图：

[图片备用地址](https://limingxie.github.io/images/algorithm/tree/bst_tree_insert.png)  
![bst_tree_insert](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/tree/bst_tree_insert.png)

```go
func (b *BSTTree) Insert(val int) {
	if b.Root == nil {
		b.Root = &TreeNode{Value: val}
		return
	}
	currentNode := b.Root
	parentNode := currentNode
	for currentNode != nil {
		parentNode = currentNode
		if currentNode.Value > val {
			currentNode = currentNode.Left
			if currentNode == nil {
				parentNode.Left = &TreeNode{Value: val}
				return
			}
		} else if currentNode.Value < val {
			currentNode = currentNode.Right
			if currentNode == nil {
				parentNode.Right = &TreeNode{Value: val}
				return
			}
		} else {
			return // currentNode.Value == val
		}
	}
}
```

#### 4.排序二叉树(Binary Search Tree)的删除

1. 从根(root)节点开始查看。值比根节点小的话查询左子树，大的话查询右子树。  
2. 到下一个节点以后，重复以上操作，直到找到要删除的节点。  
3. a) 如果删除节点没有叶节点，直接删除就行。  
   b) 如果删除节点只有左节点或右节点，给删除的parent节点链接剩余的子节点就行。  
   c) 如果删除节点左右都有子节点，则去寻找删除节点右节点的最小值(最左节点)，放到删除节点位置。  
      这个最小节点，如果有右节点需要还跟parent节点做链接。

详细的操作方式可以参考下图(下图是c的情况)：

[图片备用地址](https://limingxie.github.io/images/algorithm/tree/bst_tree_delete.png)  
![bst_tree_insert](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/tree/bst_tree_delete.png)

```go
func (b *BSTTree) Delete(val int) bool {
	if b.Root == nil {
		fmt.Println("tree is empty!")
		return false
	}
	currentNode := b.Root
	parentNode := currentNode
	isLeftChild := false

	for currentNode != nil && currentNode.Value != val {
		parentNode = currentNode
		if currentNode.Value == val {
			break
		} else if currentNode.Value > val {
			currentNode = currentNode.Left
			isLeftChild = true
		} else {
			currentNode = currentNode.Right
			isLeftChild = false
		}
	}

	if currentNode.Left == nil && currentNode.Right == nil {
		if b.Root == currentNode {
			b.Root = nil
		} else if isLeftChild {
			parentNode.Left = nil
		} else {
			parentNode.Right = nil
		}
	} else if currentNode.Left != nil && currentNode.Right == nil {
		if b.Root == currentNode {
			b.Root = currentNode.Left
		} else if isLeftChild {
			parentNode.Left = currentNode.Left
		} else {
			parentNode.Right = currentNode.Left
		}
	} else if currentNode.Left == nil && currentNode.Right != nil {
		if b.Root == currentNode {
			b.Root = currentNode.Right
		} else if isLeftChild {
			parentNode.Left = currentNode.Right
		} else {
			parentNode.Right = currentNode.Right
		}
	} else if currentNode.Left != nil && currentNode.Right != nil {
		treeNode := currentNode.GetSubTreeForDelete()
		if b.Root == currentNode {
			b.Root = treeNode
		} else if isLeftChild {
			parentNode.Left = treeNode
		} else {
			parentNode.Right = treeNode
		}
	}

	return true
}

func (node *TreeNode) GetSubTreeForDelete() *TreeNode {
	parentNode := node
	currentNode := node.Right
	for currentNode != nil {
		if currentNode.Left == nil {
			break
		}
		parentNode = currentNode
		currentNode = currentNode.Left

	}
	if currentNode == node.Right {
		parentNode.Right = currentNode.Right
		return parentNode
	} else {
		parentNode.Left = currentNode.Right
		currentNode.Left = node.Left
		currentNode.Right = node.Right
		return currentNode
	}
}
```

#### 5.前序遍历

顺序是根节点、左子树、右子树。如下图所示

[图片备用地址](https://limingxie.github.io/images/algorithm/tree/tree_pre_order.png)  
![tree_pre_order](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/tree/tree_pre_order.png)

看代码是如何实现的：

```go
func (node *TreeNode) PreOrderTraversalPrint() {
	if node == nil {
		return
	}
	fmt.Println(node.Value)
	node.Left.PreOrderTraversalPrint()
	node.Right.PreOrderTraversalPrint()
}
```

#### 6.后序遍历

顺序是左子树、右子树、根节点。如下图所示

[图片备用地址](https://limingxie.github.io/images/algorithm/tree/tree_post_order.png)  
![tree_post_order](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/tree/tree_post_order.png)

看代码是如何实现的：

```go
func (node *TreeNode) PostOrderTraversalPrint() {
	if node == nil {
		return
	}
	node.Left.PostOrderTraversalPrint()
	node.Right.PostOrderTraversalPrint()
	fmt.Println(node.Value)
}
```

#### 7.中序遍历

顺序是左子树、根节点、右子树。如下图所示

[图片备用地址](https://limingxie.github.io/images/algorithm/tree/tree_in_order.png)  
![trtree_in_orderee](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/algorithm/tree/tree_in_order.png)

看代码是如何实现的：

```go
func (node *TreeNode) InOrderTraversalPrint() {
	if node == nil {
		return
	}
	node.Left.InOrderTraversalPrint()
	fmt.Println(node.Value)
	node.Right.InOrderTraversalPrint()
}
```

**有没有发现中序遍历是把BST树按照排序打出来了？**


#### 8.执行结果

```go
func main() {
	btsTree := BSTTree{Root: &TreeNode{Value: 57}}
	btsTree.Insert(21)
	btsTree.Insert(88)
	btsTree.Insert(12)
	btsTree.Insert(36)
	btsTree.Insert(69)
	btsTree.Insert(97)
	btsTree.Insert(7)
	btsTree.Insert(14)
	btsTree.Insert(24)
	btsTree.Insert(47)
	btsTree.Insert(61)
	btsTree.Insert(73)
	btsTree.Insert(92)
	btsTree.Insert(99)

	fmt.Println("------------ Print --------------")
	btsTree.Root.PreOrderTraversalPrint()
	fmt.Println("")
	btsTree.Root.PostOrderTraversalPrint()
	fmt.Println("")
	btsTree.Root.InOrderTraversalPrint()
	fmt.Println("")

	fmt.Println("------------ Get 24 --------------")
	fmt.Printf("%+v", btsTree.Get(24))
	fmt.Println("")

	fmt.Println("----------- Insert 95 -------------")
	btsTree.Insert(95)
	btsTree.Root.PreOrderTraversalPrint()
	fmt.Println("")
	btsTree.Root.PostOrderTraversalPrint()
	fmt.Println("")
	btsTree.Root.InOrderTraversalPrint()
	fmt.Println("")

	fmt.Println("----------- Delete 88 -------------")
	btsTree.Delete(88)
	btsTree.Root.PreOrderTraversalPrint()
	fmt.Println("")
	btsTree.Root.PostOrderTraversalPrint()
	fmt.Println("")
	btsTree.Root.InOrderTraversalPrint()
	fmt.Println("")
}
```

执行结果为：
```
$ go run main.go
------------ Print --------------
57 21 12 7 14 36 24 47 88 69 61 73 97 92 99 
7 14 12 24 47 36 21 61 73 69 92 99 97 88 57
7 12 14 21 24 36 47 57 61 69 73 88 92 97 99
------------ Get 24 --------------
&{Value:24 Left:<nil> Right:<nil>}
----------- Insert 95 -------------
57 21 12 7 14 36 24 47 88 69 61 73 97 92 95 99
7 14 12 24 47 36 21 61 73 69 95 92 99 97 88 57
7 12 14 21 24 36 47 57 61 69 73 88 92 95 97 99
----------- Delete 88 -------------
57 21 12 7 14 36 24 47 92 69 61 73 97 95 99
7 14 12 24 47 36 21 61 73 69 95 99 97 92 57
7 12 14 21 24 36 47 57 61 69 73 92 95 97 99
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`