---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_搜索查找和压缩解压(09)"
date: 2022-07-25T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了搜索查找和压缩解压相关的内容。<!--more-->  

## 1.find 查找文件或者目录

find [搜索范围] [选项]  

* -name<查询方式>  按照指定的文件名查找模式查找文件
* -user<用户名> 查找属于指定用户名所有文件
* -size<文件大小> 按照指定的文件大小查找文件 (b,c,w,k,M,G)

```bash
find bb/ -name "*.txt"
find bb/ -user aa
find /home -size +204800
```

## 2.locate 快速定位文件路径

locate 搜索文件

```bash
#locate 指令基于数据库进行查询，所以第一次运行前，必须使用 updatedb 指令创建 locate 数据库。
updatedb

locate temp

which
```

## 3.grep 过滤查找及“|”管道符

grep 选项 查找内容 源文件  

* -n 显示匹配行及行号

```bash
ls | grep -n test
```

## 4.gzip/gunzip 压缩

```bash
#压缩文件，只能将文件压缩为*.gz 文件
gzip 文件

#解压缩文件命令
gunzip 文件.gz 
```

* 只能压缩文件不能压缩目录
* 不保留原来的文件
* 同时多个文件会产生多个压缩包

## 5.zip/unzip 压缩

```bash
#压缩文件和目录的命令
zip [选项] XXX.zip 将要压缩的内容

#解压缩文件
unzip [选项] XXX.zip
```

* -r 压缩目录  (zip 选项)
* -d<目录> 指定解压后文件的存放目录  (unzip 选项)

## 6.tar 打包

```bash
#打包目录，压缩后的文件格式.tar.gz
tar [选项] XXX.tar.gz 将要打包进去的内容
```

* -c 产生.tar 打包文件
* -v 显示详细信息
* -f 指定压缩后的文件名 -z 打包同时压缩
* -x 解包.tar 文件
* -C 解压到指定目录

```bash
#压缩多个文件
tar -zcvf aa.tar.gz bb.txt cc.txt

#压缩目录
tar -zcvf xiyou.tar.gz xiyou/

#解压到当前目录
tar -zxvf aa.tar.gz

#解压到指定目录
tar -zxvf xiyou.tar.gz -C /opt
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
