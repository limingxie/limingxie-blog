---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_用户和用户组(07)"
date: 2022-07-23T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了用户和用户组相关的内容。<!--more-->  

## 1.用户

#### 1.1 useradd

```bash
#添加新用户
useradd 用户名

#添加新用户到某个组
useradd -g 组名 用户名 
```

#### 1.2 passwd 设置用户密码

```bash
#设置用户密码
passwd 用户名 
```

#### 1.3 d 查看用户是否存在

```bash
#查看用户是否存在
id 用户名
```

#### 1.4 cat /etc/passwd查看创建了哪些用户

```bash
cat  /etc/passwd
```

#### 1.5 su 切换用户

```bash
#切换用户
su swith-user 

#切换用户，只能获得用户的执行权限，不能获得环境变量
su 用户名称

#切换到用户并获得该用户的环境变量及执行权限
su - 用户名称
```

#### 1.6 userdel 删除用户

```bash
#删除用户但保存用户主目录
userdel 用户名
#用户和用户主目录，都删除
userdel -r 用户名
```

#### 1.7 who 查看登录用户信息

```bash
#显示自身用户名称
whoami 
#显示登录用户的用户名以及登陆时间
who am i 
```

#### 1.8 sudo 设置普通用户具有 root 权限

```bash
#修改配置文件
vi /etc/sudoers
```

#### 1.9 usermod 修改用户

```bash
//修改用户的初始登录组，给定的组必须存在。默认组 id 是 1。
usermod -g 用户组 用户名
```

## 2.用户组

#### 2.1 groupadd 新增组

```bash
groupadd 组名

groupadd guanli
```

#### 2.2 groupdel 删除组

```bash
groupdel 组名

groupdel guanli
```

#### 2.3 groupmod 修改组

```bash
#指定工作组的新组名
groupmod -n 新组名 老组名
```

#### 2.4 cat /etc/group查看创建了哪些组

```bash
cat  /etc/group
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
