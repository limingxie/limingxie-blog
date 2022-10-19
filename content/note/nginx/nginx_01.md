---
author: "li_mingxie"
title: "【nginx笔记】nginx简介(01)"
date: 2022-10-15T23:28:49+08:00
tags: [
    "nginx",
]
categories: [
    "nginx",
]
---

这一片简单的整理了 **`nginx`** 相关的简介。<!--more-->  

## 1.常用的4个版本

* **Nginx开源版** => <http://nginx.org/>
* **Nginx plus 商业版** => <https://www.nginx.com>
* **Openresty** => <http://openresty.org>
* **Tengine** => <http://tengine.taobao.org/>

## 2.目录结构

进入Nginx的主目录我们可以看到这些文件夹

* `conf` 用来存放配置文件相关
* `html` 用来存放静态文件的默认目录 html、css等
* `sbin` vnginx的主程序
* `logs` 存放日志

## 3.启动和停止

```
./nginx           //启动
./nginx -s stop   //强制停止
./nginx -s quit   //停止(等待任务结束)
./nginx -s reload //重新加载配置
```

## 4.两个个进程

启动nginx时，会启用2中进程。一个是master进程，一个worker进程。  

**`master进程`** 是主进程，主要用来管理worker进程。  
包括接收来自外界的信号，向各worker进程发送信号。  
监控worker进程的运行状态，当worker进程退出后(异常情况下)，会自动重新启动新的worker进程。  

**`worker进程`** 是工作进程  
conf中的 worker _processes 可以配置多个worker进程, 一般会设置与机器cpu核数一致。  

## 5.Nginx基础配置

nginx/conf/nginx.conf文件基本配置信息

```
#nginx work进程数, 一般不会超过CPU个数
worker_processes  1;

events {
    #单个业务进程可接受连接数
    worker_connections  1024;
}

http {
    #引入http mime类型
    include       mime.types;
    #如果mime类型没匹配上, 默认使用二进制流的方式传输。
    default_type  application/octet-stream;

    #用linux的sendfile(socket, file, len)高效网络传输, 开启数据0拷贝。
    sendfile        on;

    #keepalive的超时时间
    keepalive_timeout  65;

    server {
        #监听80端口
        listen       80;
        #主机名
        server_name  localhost;

        #匹配路径
        location / {
            root   html;    #文件目录
            index  index.html index.htm;    #默认页
        }

        #报错编码对应的页
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
