---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_磁盘查看和分区(10)"
date: 2022-07-26T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了磁盘查看和分区相关的内容。<!--more-->  

## 1.du 查看文件和目录占用的磁盘空间

```bash
#显示目录下每个子目录的磁盘使用情况
du 目录/文件

du -sh
```

* -h 以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示; -a 不仅查看子目录大小，还要包括文件
* -c 显示所有的文件和子目录大小后，显示总和
* -s 只显示总和
* --max-depth=n 指定统计子目录的深度为第 n 层

## 2.df 查看磁盘空间使用情况

```bash
#列出文件系统的整体磁盘使用量，检查文件系统的磁盘空间占用情况
df 选项 

df -h

free -h
```

* -h 以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示

## 3.lsblk 查看设备挂载情况

```bash
#查看设备挂载情况
lsblk 
```

* -f 查看详细的设备挂载情况，显示文件系统信息

## 4.mount/umount 挂载/卸载

```bash
#挂载设备
mount [-t vfstype] [-o options] device dir

#卸载设备
umount 设备文件名或挂载点
```

* -t vfstyp  
    指定文件系统的类型，通常不必指定。mount 会自动选择正确的类 型。常用类型有:  
    光盘或光盘镜像:iso9660  
    DOS fat16 文件系统:msdos  
    Windows 9x fat32 文件系统:vfat  
    Windows NT ntfs 文件系统:ntfs  
    Mount Windows 文件网络共享:smbfs  
    UNIX(LINUX) 文件网络共享:nfs  

* -o options  
    主要用来描述设备或档案的挂接方式。常用的参数有:  
    loop:用来把一个文件当成硬盘分区挂接上系统  
    ro:采用只读方式挂接设备 rw:采用读写方式挂接设备  
    iocharset:指定访问文件系统所用字符集  

* device  
    要挂接(mount)的设备  

* dir  
    设备在系统上的挂接点(mount point)  

#### 设置开机自动挂载

```bash
vi /etc/fstab
```

## 5.fdisk 分区

```bash
#查看磁盘分区详情
fdisk -l 

#对新增硬盘进行分区操作
fdisk硬盘设备名
```

* -l 显示所有硬盘的分区列表

* (1)Linux 分区
    Device:分区序列  
    Boot:引导  
    Start:从X磁柱开始  
    End:到Y磁柱结束  
    Blocks:容量  
    Id:分区类型ID  
    System:分区类型  

* (2)分区操作按键说明  
    m:显示命令列表  
    p:显示当前磁盘分区  
    n:新增分区  
    w:写入分区信息并退出  
    q:不保存分区信息直接退出  

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
