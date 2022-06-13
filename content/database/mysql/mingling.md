---
author: "li_mingxie"
title: "mysql常用命令"
date: 2222-03-27T07:28:49+08:00
tags: [
    "mysql",
]
categories: [
    "docker"
]
---

show create database test
show create tatble test
show varibles like '%version%'
show varibles like '%character_%'
show varibles like '%collation_%'

需要修改my.ini文件
[mysql]
default-character-set=utf8 #默认字符集

[mysqld]
character-set-server=utf8 #字符集
collation-server=utf8_general_ci #排序规则

8.0版本有2种类密码验证的方式。

改成 5.7版本的密码验证方式

```mysql
use mysql;

#修改永红密码登录的规则
ALTER USER `root`@`localhost` IDENTIFIED WITH mysql_native_password BY '123456';  

#刷新权限
FLUSH PRIVILEGES;

````

执行脚本
source ./xxx.sql;

关掉自动提交
set autocommit=false;

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`
