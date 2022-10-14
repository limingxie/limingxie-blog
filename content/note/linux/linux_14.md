---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_shell流程控制(14)"
date: 2022-07-29T23:40:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了Shell流程控制相关的内容。<!--more-->  

## 1.条件判断

**1.test condition**  
**2.[ condition ]**(注意 condition 前后要有空格)  

#### 1.1 两个整数之间比较

* -eq 等于(equal)
* -lt 小于(less than)
* -gt 大于(greater than)
注:如果是字符串之间的比较 ，用等号“=”判断相等;用“!=”判断不等。

#### 1.2 按照文件权限进行判断

* -r 有读的权限(read)
* -w 有写的权限(write)
* -x 有执行的权限(execute)

#### 1.3 按照文件类型进行判断

* -e 文件存在(existence)
* -f 文件存在并且是一个常规的文件(file)
* -d 文件存在并且是一个目录(directory)

## 2.if判断

```bash
if [ 条件判断式 ]
then
    程序
elif [ 条件判断式 ]
then
    程序
else
    程序
fi
```

* [ 条件判断式 ]，中括号和条件判断式之间必须有空格
* if 后要有空格

## 3.case 语句

```bash
case $变量名 in 
"值 1")
    如果变量的值等于值 1，则执行程序 1
;;
"值 2")
    如果变量的值等于值 2，则执行程序 2
;;
    ...省略其他分支...
*) 
    如果变量的值都不是以上的值，则执行此程序
;;
esac
```

* case 行尾必须为单词“in”，每一个模式匹配必须以右括号“)”结束。
* 双分号“;;”表示命令序列结束，相当于 java 中的 break。
* 最后的“*)”表示默认模式，相当于 java 中的 default。

## 4.for 循环

```bash
for (( 初始值;循环控制条件;变量变化 )) do
    程序
done

sum=0
for((i=0;i<=100;i++))
do
       sum=$[$sum+$i]
done
echo $sum
```

```bash
for 变量 in 值 1 值 2 值 3...
do
    程序
done

for i in cls mly wls
do   echo "ban zhang love $i"
done
```

## 5.while 循环

```bash
while [ 条件判断式 ] do
    程序
done

sum=0
i=1
while [ $i -le 100 ]
do
       sum=$[$sum+$i]
       i=$[$i+1]
done
```

## 6.read 读取控制台输入

```bash
read (选项) (参数)
```

1选项

* -p:指定读取值时的提示符;
* -t:指定读取值时等待的时间(秒)如果-t 不加表示一直等待

2参数

* 变量:指定读取值的变量名

## 7.函数

#### 7.1 系统函数

```bash
basename [string / pathname] [suffix]

$ basename /home/xx/rizhi.txt .txt
rizhi

dirname 文件绝对路径

$ dirname /home/xx/rizhi.txt
/home/xx
 
```

#### 7.2 自定义函数

```bash
[ function ] funname[()] 
{
    Action;
    [return int;] 
}
```

* 必须在调用函数地方之前，先声明函数，shell 脚本是逐行运行。不会像其它语言一样先编译。
* 函数返回值，只能通过$?系统变量获得，可以显示加:return 返回，
如果不加，将以最后一条命令运行结果，作为返回值。return 后跟数值 n(0-255)

## 其他

一个在jenkins里用到的简单例子

```shell
#!/bin/bash

#删除历史数据
#rm -rf xxxx

##获取传入参数
appname=$1
echo "arg:$1"

#获取正在云心的jar包的pid
pid=`ps -ef | grep $1 | grep 'java -jar' | awk '{printf $2}'`

echo $pid

#如果pid为空，提示一下，否则执行kill
#使用 -z 做空值判断
if [ -z $pid ];
    then
        echo "$appname not started"
    else
        kill -9 $pid
        echo "$appname stopping..."

check=`ps -ef | grep -w $pid | grep java`
if [ -z $check ];
    then
        echo "$appname pid:$pid is stop"
    else 
        echo "$appname stop failed"
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
