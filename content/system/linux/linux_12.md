---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_软件包管理(11)"
date: 2022-07-28T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了软件包管理相关的内容。<!--more-->  

## 1.RPM

RPM(RedHat Package Manager)

#### 1.2 RPM 查询命令

```bash
#查询所安装的所有 rpm 软件包
rpm -qa
```

由于软件包比较多，一般都会采取过滤。rpm -qa | grep rpm软件包  

#### 1.3 RPM 卸载命令

```bash
rpm -e RPM软件包
rpm -e --nodeps 软件包
```

* -e 卸载软件包
* --nodeps 卸载软件时，不检查依赖。这样的话，那些使用该软件包的软件在此之后可能就不能正常工作了。

#### 1.4 RPM 安装命令

```bash
rpm -ivh RPM 包全名
```

* -i install，安装
* -v --verbose，显示详细信息
* -h --hash，进度条
* --nodeps 安装前不检查依赖

## 2.YUM

YUM(Yellow dog Updater, Modified)

```bash
yum [选项] [参数]
```

* -y 对所有提问都回答“yes”

* install   安装 rpm 软件包
* update    更新 rpm 软件包
* check-update  检查是否有可用的更新 rpm 软件包
* remove    删除指定的 rpm 软件包
* list  显示软件包信息
* clean 清理 yum 过期的缓存
* deplist   显示 yum 软件包的所有依赖关系

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
