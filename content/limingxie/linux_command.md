---
author: "li_mingxie"
title: "linux 常用命令整理(一)"
date: 2018-09-02T07:28:49+08:00
tags: [
    "linux",
]
categories: [
    "linux",
]
---

这么晚了才认识了linux系统。感觉越来越喜欢linux系统了。  
废话少说，这几天自己做的笔记，炫耀一下....^^<!--more-->  
方便以后用的时候查找。  


<table class="table-bordered table-striped table-condensed ">
    <tr>
        <th><font style="font-family:SimHei">命令行</font></th>
	    <th><font style="font-family:SimHei">备注</font></th>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">pwd:</font></td>
	    <td><font style="font-family:SimHei">改变目录        cd..</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">ls:</font></td>
	    <td><font style="font-family:SimHei">列出目录内容    ls -l   =   ll <br/>
        ls -l   t    时间排序  S 大小排序     -d  目录 <br/>
        ls -lt  --reverse <br/>
        ls -F 所有文件 <br/>
        Ls -i  查看所有节点inodemv </font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">file:</font></td>
	    <td><font style="font-family:SimHei">确定文件类型</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">less:</font></td>
	    <td><font style="font-family:SimHei">查看文件内容</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">cp:</font></td>
	    <td><font style="font-family:SimHei">复制文件和目录  -a  复制所有文件一起权限   -r  递归复制  -u 复制没有或更新的内容  <br/>
            cp file1 file2 dir1 复制文件file1和file2 到dir1</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">mv:</font></td>
	    <td><font style="font-family:SimHei">移动或重命名文件夹和目录 -i  操作需要做确认 -u  -v 显示信息   <br/>
            mv file1 file2  file1移动到file2 或重命名file2   <br/>
            mv file1 file2 dir1 将file1,file2 移动到dir1 前提是dir1必须存在</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">mkdir:</font></td>
	    <td><font style="font-family:SimHei">创建目录</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">rm:</font></td>
	    <td><font style="font-family:SimHei">移除文件盒目录
            危险的命令，先用ls 看看是不是你要删除的文件，在换成rm执行。   <br/>
            -i 需要确认删除  -r 递归删除 -f --force 忽略提示全部删除 -v 查看信息   <br/>
            rm -rf file1 dir1  删除 file1和dir2 并且不提示执行删除</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">ln:</font></td>
	    <td><font style="font-family:SimHei">创建硬链接和符号链接   <br/>
            in file link 创建硬链接   <br/>
            in -s item link 创建符号链接，这里item可以是目录</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">通配符</font></td>
	    <td><font style="font-family:SimHei">g*  g开头的任意文件   <br/>
            b*.txt  b开头的 .txt文件   <br/>
            Da???   Da开头的后面有3个字符的文件   <br/>
            [abc]*   abc中任一个开头的文件   <br/>
            BA.[0-9][0-9]  BA.开头的后面紧跟着3个数字的文件   <br/>
            [[:upper]]*   以大写字母开头的文件   <br/>
            [![:digit]]*   不是以数字开头的文件   <br/>
            *[[:lower:]123]  以小写字母或数字1，2，3中的任一个结尾的文件</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">type:</font></td>
	    <td><font style="font-family:SimHei">解释命令语   type cp    type cd</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">which:</font></td>
	    <td><font style="font-family:SimHei">显示执行哪个执行程序   which cp</font></td>
    </tr>
     <tr>
        <td><font style="font-family:SimHei">info:</font></td>
	    <td><font style="font-family:SimHei">显示命令的info条目</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">whatis:</font></td>
	    <td><font style="font-family:SimHei">一个命令的简述</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">alias:</font></td>
	    <td><font style="font-family:SimHei">创建命令的别名  <br/>
            alias name=’string’  <br/>
            unalias name</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">cat:</font></td>
	    <td><font style="font-family:SimHei">合并文件 <br/>
            cat filename filename … > filename</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">sort:</font></td>
	    <td><font style="font-family:SimHei">对文本行排序</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">uniq:</font></td>
	    <td><font style="font-family:SimHei">报告或删除文件中重复的行</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">wc:</font></td>
	    <td><font style="font-family:SimHei">打印文件中的换行符，字和字节的个数</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">grep:</font></td>
	    <td><font style="font-family:SimHei">打印匹配行  -i 忽略大小写  -v 输出不匹</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">head:</font></td>
	    <td><font style="font-family:SimHei">输出文件第一部分内容  head -n 5  <br/> ls-output.txt</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">tail:</font></td>
	    <td><font style="font-family:SimHei">输出文件最后一部分内容   tail -n 5 ls-output.txt  <br/> 
            ls /usr/bin | tail -n 5  <br/> 
            tail -f /var/log/messages  -f 选项是实时监控文件</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">tee:</font></td>
	    <td><font style="font-family:SimHei">读取标准输入的数据，并将其内容输出到标准输出和文件中  <br/> 
            ls /usr/bin | tee ls.txt | grep zip</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">>   >></font></td>
	    <td><font style="font-family:SimHei">输出重定向  <br/>
            ls -l /usr/bin > ls-output.txt  输出内容保存       到 ls-output.txt 文件中   <br/>
            ls -l /usr/bin >> ls-output.txt  输出内容保存到 ls-output.txt 文件尾部继续保存   <br/>
            0 1 2 输入，输出，错误的 重定向  <br/>
            ls -l /bin/usr 2> ls-error.txt  错误内容重定向到 ls-error.txt 文件中  <br/>
                <br/>
            ls -l /usr/bin > ls-output.txt 2>&1  ls -l /usr/bin >> ls-output.txt 2>&1  <br/>
            ls -l /usr/bin &> ls-output.txt  输出和错误都重定向到 ls-output.text文件中  <br/>
            <br/>
            ls -l /usr/bin 2> /dev/null   把错误指向/dev/null的位桶(bit bucket)</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei"> |  管道</font></td>
	    <td><font style="font-family:SimHei">ls /usr/bin | sort(排序) | uniq(去重) -d(显示重复) | less    <br/>
            ls /usr/bin | sort(排序) | uniq(去重) | wc -l   </font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">echo:</font></td>
	    <td><font style="font-family:SimHei">显示一行文本  echo *   echo ls*    <br/>
            echo [[:upper]]*   echo /usr/*/share    <br/>
            ~  指定主目录    <br/>
            echo $((2+2))      echo $(($((5 * 2)) + 3))    <br/>
            echo Front-{A,D,E}-back    echo  Number_{1..5}    echo  {Z..A}    <br/>
            echo a{A{1,2},B{3,4}}b    <br/>
            mkdir {2009..2011}-0{1..9}  {2009-2011}-{10..12}    <br/>
            ls    <br/>
            printenv | less   打印环境变量    <br/>
            ls -l $(which cp)    ls -l `which cp`   <br/>
            echo "$USER $((2+2)) $(cal)”   打印日期  <br/>
            echo ‘$USER $((2+2))’ 全部如实打印</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">\   \\</font></td>
	    <td><font style="font-family:SimHei">echo "The balance for user $USER is: \$5.00”  引用单个符号 + \   <br/>
            需要用 \ 字符  用 \\   <br/>
            \a  响铃   \b  退格  \n  新一行  \r  回车  \t  制表</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">clear:</font></td>
	    <td><font style="font-family:SimHei">清屏</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">history:</font></td>
	    <td><font style="font-family:SimHei">显示历史列表的记录   <br/>
            history | less   <br/>
            history | grep /usr/bin    !88  执行第88行命令    <br/>
            !number   第number行里的命令   <br/>
            !string  string开头的命令   <br/>
            !?string 最近包含的命令</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">id:</font></td>
	    <td><font style="font-family:SimHei">显示用户身份标识    <br/>
            用户账户： /etc/passwd   /etc/shadow    <br/>
            用户组： /etc/group</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">chmod:</font></td>
	    <td><font style="font-family:SimHei">更改文件模式   <br/>
            chmod 777 filename   <br/>
            u 用户  g 群组  o 其他  a all   <br/>
            u+x u-x  +x o-rw  go=rw  u+x, go=rx</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">umask:</font></td>
	    <td><font style="font-family:SimHei">设置文件的文人权限</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">su:</font></td>
	    <td><font style="font-family:SimHei">以另一个用户的身份运行shell</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">sudo:</font></td>
	    <td><font style="font-family:SimHei">以另一个用户身份执行命令</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">chown:</font></td>
	    <td><font style="font-family:SimHei">更改文件所有者   <br/>
            chown bob   <br/>
            chown bob:users   <br/>
            chown :admins</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">chgrp:</font></td>
	    <td><font style="font-family:SimHei">更改文件所属群组</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">password:</font></td>
	    <td><font style="font-family:SimHei">更改用户密码</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">ps:</font></td>
	    <td><font style="font-family:SimHei">显示党项所有的进程运行情况、   <br/>
            ps x      ps  aux   <br/>
            R  运行状态  S 睡眠状态  D  不可中断状态  T 暂停状态  Z 无效或是“僵尸”进程      <br/>
            < 高优先进程  N 低优先进程   <br/>
            <br/>
            %MEM  内存使用百分比   <br/>
            VSZ  虚拟耗用内存大小   <br/>
            RSS  实际使用内存大小(KB为单位)</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">top:</font></td>
	    <td><font style="font-family:SimHei">实时显示当前所理由任务的资源占用情况</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">jobs:</font></td>
	    <td><font style="font-family:SimHei">列出所有活动作业的状态信息</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">bg:</font></td>
	    <td><font style="font-family:SimHei">设置在后台中运行作业</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">fg:</font></td>
	    <td><font style="font-family:SimHei">设置在前台中运行作业</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">kill:</font></td>
        <td><font style="font-family:SimHei">发送信号给某个进程</font></td>
	    <td><font style="font-family:SimHei"></font></td>
    </tr>
    <tr>
	    <td><font style="font-family:SimHei">killall:</font></td>
        <td><font style="font-family:SimHei">杀死指定名字的进程</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">shutdown:</font></td>
	    <td><font style="font-family:SimHei">关机或者重启系统</font></td>
    </tr>
</table>
<br/>
<br/>

<table class="table-bordered table-striped table-condensed ">
    <tr>
        <th><font style="font-family:SimHei">快捷键</font></th>
	    <th><font style="font-family:SimHei">备注</font></th>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">Ctrl + A:</font></td>
	    <td><font style="font-family:SimHei">移动光标到行首</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">Ctrl + E:</font></td>
	    <td><font style="font-family:SimHei">移动光标到行尾</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">Ctrl + K:</font></td>
	    <td><font style="font-family:SimHei">剪切光标到行尾的文本</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">Ctrl + U:</font></td>
	    <td><font style="font-family:SimHei">剪切光标到行首的文本</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">Ctrl + Y:</font></td>
	    <td><font style="font-family:SimHei">缓冲区的文本粘贴到光标位置</font></td>
    </tr>
    <tr>
        <td><font style="font-family:SimHei">Ctrl + R:</font></td>
	    <td><font style="font-family:SimHei">搜索之前的命令  history | grep   相似</font></td>
    </tr>
</table>

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`