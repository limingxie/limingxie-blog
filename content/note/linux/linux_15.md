---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_正则(14)"
date: 2022-07-30T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了正则相关的内容。<!--more-->  

## 1.正则

* ^ 匹配一行的开头
* $ 匹配一行的结束
* . 匹配一个任意的字符
* \* 不单独使用，他和上一个字符连用，表示匹配上一个字符 0 次或多次
* [ ] 表示匹配某个范围内的一个字符
* \ 表示转义，并不会单独使用

## 2.cut

```bash
cut [选项参数] filename
```

* -f 列号，提取第几列
* -d 分隔符，按照指定分隔符分割列，默认是制表符“\t”
* -c 按字符进行切割 后加加 n 表示取第几列 比如 -c 1

```bash
cut -d " " -f 2,3 cut.txt
wo shi 
ni shi
```

## 3.awk

```bash
awk [选项参数] ‘/pattern1/{action1} /pattern2/{action2}...’ filename
```

* pattern:表示 awk 在数据中查找的内容，就是匹配模式
* action:在找到匹配内容时所执行的一系列命令

* -F 指定输入文件分隔符
* -v 赋值一个用户定义变量

```bash
$ awk -F : '{print "filename:" FILENAME ",linenum:" NR ",col:"NF}' passwd
filename:passwd,linenum:1,col:7
filename:passwd,linenum:2,col:7
filename:passwd,linenum:3,col:7
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
