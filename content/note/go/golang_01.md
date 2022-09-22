---
author: "li_mingxie"
title: "【golang笔记】go_概述(1)"
date: 2022-09-02T23:28:49+08:00
tags: [
    "golang",
]
categories: [
    "golang",
]
---

这一篇简单的整理了golang的基础内容。<!--more-->

## 1. 环境变量

刚接触的时候安装时环境变量的配置，有可能制造一些小麻烦。

|环境变量|说明|
|---|---|
|GOROOT|指定SDK的安装路径|
|PATH|添加SDK的/bin目录|
|GOPATH|工作目录,将来我们的go项目的工作路径|

```bash
export GOROOT=$HOME/go  #=>后续版本不需要配置 
export PATH=/usr/local/go/bin
export GOPATH=$HOME/projects/gopath
```

## 2.go build

```bash
$go run main.go 

$go build && ./test

#编译文件起别名
$go build -o mytest main.go 
```

## 3.gofmt

//手动格式化文件。

```bash
$gofmt -w main.go
```

## 官网，中文说明文档

<https://go.dev/>
<https://studygolang.com/pkgdoc>

## 一次性声明多个变量

```go
var mun, name, age = 1, "tom", 12
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
