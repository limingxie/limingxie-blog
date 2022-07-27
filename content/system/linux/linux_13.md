---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_shell基础(13)"
date: 2022-07-29T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了Shell概述相关的内容。<!--more-->  

## 1.Shell 概述

Shell是一个命令行解释器。

#### 1.1 查看Linux 提供的 Shell 解析器

```bash
$ cat /etc/shells

/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
/bin/tcsh
/bin/csh
```

#### 1.2 bash 和 sh 的关系

```bash
$ ll | grep bash
-rwxr-xr-x.   1 root root      964536 Apr  1  2020 bash
lrwxrwxrwx.   1 root root          10 Sep 14  2020 bashbug -> bashbug-64
-rwxr-xr-x.   1 root root        6964 Apr  1  2020 bashbug-64
lrwxrwxrwx.   1 root root           4 Sep 14  2020 sh -> bash
```

## 2.脚本编写

脚本以#!/bin/bash 开头(指定解析器)  

```bash
$ touch helloworld.sh
$ vim helloworld.sh

#编写一下内容
#!/bin/bash
echo "helloworld"
```

```bash
#执行方式1 打开子bash 执行
$ sh ./helloworld.sh
$ bash ./helloworld.sh

#执行方式2 给helloworld.sh执行权限(x)
$ ./helloworld.sh

#执行方式3 使用内嵌命令 source 或 .
$ source helloworld.sh
$ . helloworld.sh
```

## 3.变量

```bash
#查看一个系统变量
echo $HOME
#查看所有系统变量
env

#查看Shell 中所有变量
set

#定义变量，注意，=号前后不能有空格
变量名=变量值

#撤销变量
unset 变量名

#声明静态变量:不能 unset
readonly 变量
```

* 变量名称可以由字母、数字和下划线组成，但是不能以数字开头，环境变量名建议大写。
* 等号两侧不能有空格
* 在bash中，变量默认类型都是字符串类型，无法直接进行数值运算。
* 变量的值如果有空格，需要使用双引号或单引号括起来。

#### 特殊变量

* $n n 为数字，$0 代表该脚本名称，$1-$9 代表第一到第九个参数，十以上的参数，十以上的参数需要用大括号包含，如${10}  
* $# 获取所有输入参数个数，常用于循环,判断参数的个数是否正确以及加强脚本的健壮性。  

* $*这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体  
* $@ 这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待  

* $? 最后一次执行的命令的返回状态。如果这个变量的值为 0，证明上一个命令正确执行;  
如果这个变量的值为非 0(具体是哪个数，由命令自己来决定)，则证明 上一个命令执行不正确了。  

## 4.运算符

“$((运算式))” 或 “$[运算式]”  

```bash
S=$[(2+3)*4]

echo $S
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
