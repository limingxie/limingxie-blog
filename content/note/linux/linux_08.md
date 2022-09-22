---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_文件权限(08)"
date: 2022-07-24T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了用户和用户组相关的内容。<!--more-->  

## 1.文件属性

[图片备用地址](https://limingxie.github.io/images/system/linux/rwx_01.png)  
![rwx_01](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/linux/rwx_01.png)

* \- 代表文件
* d 代表目录
* l 链接文档(link file);
* b, c  代表设备

#### rwx作用到文件

[ r ]代表可读(read): 可以读取，查看  
[ w ]代表可写(write): 可以修改，但是不代表可以删除该文件，删除一个文件的前提条件是对该文件所在的目录有写权限，才能删除该文件。  
[ x ]代表可执行(execute):可以被系统执行  

#### rwx作用到目录

[ r ]代表可读(read): 可以读取，ls查看目录内容  
[ w ]代表可写(write): 可以修改，目录内创建+删除+重命名目录  
[ x ]代表可执行(execute):可以进入该目录  

[图片备用地址](https://limingxie.github.io/images/system/linux/rwx_02.png)  
![rwx_02](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/system/linux/rwx_02.png)

(1) 如果查看到是文件:链接数指的是硬链接个数。  
(2) 如果查看的是文件夹:链接数指的是子文件夹个数。  

## 2.chmod 改变权限

```bash
#u:所有者 g:所有组 o:其他人 a:所有人(u、g、o的总和)
chmod [{ugoa}{+-=}{rwx}]文件或目

chmod u+x aa.txt
chmod u-x,o+x aa.txt

#r=4 w=2 x=1 rwx=4+2+1=7
chmod [mode=421 ] [文件或目录]

chmod 777 aa.txt
chmod -R 777 bb/
```

## 3.chown 改变所有者

```bash
#改变文件或者目录的所有者
chown [选项] [最终用户] [文件或目录]

chown atguigu aa.txt
chown -R atguigu:atguigu bb/
```

## 4.4 chgrp 改变所属组

```bash
#改变文件或者目录的所属组
chgrp [最终用户组] [文件或目录]
chgrp root aa.txt
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
