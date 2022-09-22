---
author: "li_mingxie"
title: "【golang笔记】go_命令行参数(20)"
date: 2022-09-21T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang **`命令行参数`** 相关的内容。<!--more-->

## 1.os.Args

os.Args是一个string的切片，用来存储命令参数。  

```go
package main
import (
 "fmt"
 "os"
)

func main() {
 fmt.Println("命令行的参数有", len(os.Args))
 //遍历os.Args切片，就可以得到所有的命令行输入参数值
 for i, v := range os.Args {
  fmt.Printf("args[%v]=%v\n", i, v)
 }
}
```

```bash
$ go run main.go haha test
命令行的参数有 3
args[0]=/var/folders/wn/wb4ns1vd5nl988yvr4r6fm6c0000gn/T/go-build1378197574/b001/exe/main
args[1]=haha
args[2]=test
```

## 2.flag包

```go
package main
import (
 "fmt"
 "flag"
)

func main() {

 //定义几个变量，用于接收命令行的参数值
 var user string
 var pwd string
 var host string
 var port int

 //&user 就是接收用户命令行中输入的 -u 后面的参数值
 //"u" ,就是 -u 指定参数
 //"" , 默认值
 //"用户名,默认为空" 说明
 flag.StringVar(&user, "u", "", "用户名,默认为空")
 flag.StringVar(&pwd, "pwd", "", "密码,默认为空")
 flag.StringVar(&host, "h", "localhost", "主机名,默认为localhost")
 flag.IntVar(&port, "port", 3306, "端口号，默认为3306")
 //这里有一个非常重要的操作,转换， 必须调用该方法
 flag.Parse()

 //输出结果
 fmt.Printf("user=%v pwd=%v host=%v port=%v", 
  user, pwd, host, port)
}
```

```bash
$ go run main.go -u root -pwd 1234 -h 127.0.0.1   
user=root pwd=1234 host=127.0.0.1 port=3306
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
