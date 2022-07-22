---
author: "li_mingxie"
title: "【linux笔记】linux入门简介_文件操作命令(06)"
date: 2022-07-22T23:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux"
]
---

这一篇整理了文件操作相关的内容。<!--more-->  

#### 1. pwd

显示当前工作目录的绝对路径

#### 2. Ls

```bash
ls
ls -l   ==  ll
ls -a
```

#### 3. cd

```bash
cd 路径
#回到上一次所在目录
cd -    
#跳转到实际物理路径，而非快捷方式路径
cd -P
```

#### 4. mkdir,rmdir

mkdir [选项] 要创建的目录

```bash
#创建一个目录
mkdir [选项] 要创建的目录

#创建一个多级目录
mkdir -p xiyou/dssz/meihouwang
```

rmdir:Remove directory 移除目录

```bash
rmdir root/like/file
```

#### 5. touch

touch 文件名称

#### 6. cp 复制文件或目录

cp [选项] source dest  =>  复制source文件到dest
cp -r 递归复制整个文件夹

#### 7. rm 删除文件或目录

rm [选项] deleteFile

* -r 递归删除目录中所有内容
* -f 强制执行删除操作，而不提示用于进行确认。
* -v 显示指令的详细执行过程

#### 8. mv 移动文件与目录或重命名

```bash
#重命名
mv oldNameFile newNameFile
#移动文件
mv /temp/movefile /targetFolder
```

#### 9. cat 查看文件内容

cat [选项]要查看的文件

* -n 显示所有行的行号，包括空行。

#### 10. more 文件内容分屏查看器

more 要查看的文件

* 空白键 (space)  代表向下翻一页;
* Enter   代表向下翻『一行』;
* q   代表立刻离开 more ，不再显示该文件内容。
* Ctrl+F  向下滚动一屏
* Ctrl+B  返回上一屏
* =   输出当前行的行号
* :f  输出文件名和当前行的行号

#### 11. less 分屏显示文件内容

less 要查看的文件

* 空白键    向下翻动一页;
* [pagedown] 向下翻动一页
* [pageup] 向上翻动一页;
* /字串     向下搜寻『字串』的功能;n:向下查找;N:向上查找;
* ?字串     向上搜寻『字串』的功能;n:向上查找;N:向下查找;
* q     离开 less 这个程序;

#### 12. echo

echo [选项] [输出内容]  

* -e: 支持反斜线控制的字符转换  
  * \\\\ 输出\本身  
  * \n 换行符  
  * \t 制表符，也就是 Tab 键  

#### 13. head 显示文件头部内容

```bash
#查看文件头10行内容
head 文件

#查看文件头5行内容，5可以是任意行数
head -n 5 文件
```

#### 14. tail 输出文件尾部内容

tail 文件  
tail -n 5 文件  
tail -f 文件  

```bash
# -n<行数> 输出文件尾部n行的内容
tail -n 1 smartd.conf

# -f 显示文件最新追加的内容，监视文件变化
tail -f houge.txt
```

#### 15. > 输出重定向和 >> 追加

```bash
#列表的内容写入文件a.txt中(覆盖写)
ls-l >文件

#列表的内容追加到文件aa.txt的末尾
ls-al >>文件

#将文件 1 的内容覆盖到文件 2
cat 文件 1 > 文件 2
echo “内容” >> 文件
```

#### 16. ln 软链接

```bash
#给原文件创建一个软链接
ln -s [原文件或目录] [软链接名]
```

rm -rf 软链接名，而不是 rm -rf 软链接名/  
**如果使用 rm -rf 软链接名/ 删除，会把软链接对应的真实目录下内容删掉**

#### 17. history 查看已经执行过历史命令

```bash
#查看已经执行过历史命令
history

#删除记录
history -c
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
