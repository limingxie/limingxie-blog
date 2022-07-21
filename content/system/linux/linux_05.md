---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_帮助命令和时间日期命令(05)"
date: 2022-07-21T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了帮助命令和日期相关的内容。<!--more-->  

## 1.man, help

```bash
#查看内置命令
man cd
#查看内置命令
help cd 
#查看第三方命令
man ls
#查看第三方命令
ls --help
```

常用命令

```bash
#停止进程
ctrl + c
#清屏，等同于 clear;彻底清屏是:reset
ctrl+l
```

## 2.操作日期时间命令

#### 2.1 基本语法

date [OPTION]... [+FORMAT]

```bash
#显示当前时间
date
#显示当前年份
date +%Y 
#显示当前月份
date +%m 
#显示当前是哪一天
date +%d
#显示年月日时分秒
date "+%Y-%m-%d %H:%M:%S"

#显示前一天时间
date -d '1 days ago'

#显示明天时间
date -d '-1 days ago'
```

#### 2.2 设置系统时间

date -s 字符串时间

```bash
date -s "2022-07-21 20:52:18"
```

#### 2.3 cal 查看日历

cal [选项] 不加选项，显示本月日历  

```bash
cal 2017
```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
