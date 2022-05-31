---
author: "li_mingxie"
title: "【Nodejs笔记】nodejs入门简介_nvm"
date: 2022-05-29T09:05:49+08:00
tags: [
    "Nodejs",
    "express",

]
categories: [
    "Nodejs",
]
---

nvm可以自由的管理nodejs的版本的切换，用起来非常方便。  
这篇整理了nvm安装和常用命令。  

## 1.安装

最新的安装方法可以参考这里。
<https://github.com/nvm-sh/nvm>

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

建议把脚本下载到本地自己运行安装。

## 2.常用命令

```bash
//查看版本
nvm --version

//可安装版本查询
nvm ls-remote | grep v16

//特定版本安装
nvm install v14.15.4

//node版本确认
node -v

//已安装版本确认
nvm ls

//切换版本
nvm use v16.15.0
node -v

//设置默认使用版本
nvm alias default v16.15.0
nvm ls
node -v

//查看当前版本
nvm current

```

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
