---
author: "li_mingxie"
title: "【docker笔记】docker_常用命令"
date: 2022-07-31T23:28:49+08:00
tags: [
    "docker",
]
categories: [
    "docker"
]
---

这一篇整理了docker的常用命令。<!--more-->  

## 1.镜像管理

#### 1.1 docker images

```bash
#列出镜像
docker images [options]
```

* -a :列出本地所有的镜像（含中间映像层）
* -f :显示满足条件的镜像；
* -q :只显示镜像ID。
* --digests :显示镜像的摘要信息；
* --no-trunc :显示完整的镜像信息；

#### 1.2 docker search

```bash
# 查询容器镜像
docker search xxx
```

-s 查看搜藏数大于指定数量的镜像

#### 1.3 docker pull

```bash
# 拉取容器镜像
docker pull xxx:[tag]默认 latest
```

#### 1.4 docker rmi

```bash
# 删除容器镜像
docker rmi xxx

docker rmi -f $(docker images -qa)
```

## 2.容器启动和关闭

#### 2.1 docker run

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

* --name=为容器指定一个名称；
* -d: 后台运行容器，并返回容器ID；
* -i: 以交互模式运行容器，通常与 -t 同时使用；
* -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
* -h "mars": 指定容器的hostname；
* -e mysql_con="xxxxx": 设置环境变量；
* -P: 随机端口映射，容器内部端口随机映射到主机的端口
* -p: 指定端口映射，格式为：主机(宿主)端口:容器端口
* --volume , -v: 绑定一个卷

#### 2.2 docker start/stop/restart

```bash
docker start [OPTIONS] CONTAINER [CONTAINER...]
docker stop [OPTIONS] CONTAINER [CONTAINER...]
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```

#### 2.3 docker rm

```bash
#删除一个或多个容器。
docker rm [OPTIONS] CONTAINER [CONTAINER...]

#杀掉一个运行中的容器
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```

* -f :通过 SIGKILL 信号强制删除一个运行中的容器。
* -l :移除容器间的网络连接，而非容器本身。
* -v :删除与容器关联的卷。

## 3.容器管理

#### 3.1 docker ps

```bash
#列出容器
docker ps [OPTIONS]
```

* -a :显示所有的容器，包括未运行的。
* -f :根据条件过滤显示的内容。
* -q :静默模式，只显示容器编号。
* -s :显示总的文件大小。
* --no-trunc :不截断输出。

#### 3.2 docker stats

```bash
# 显示容器资源的使用情况
docker stats [OPTIONS] [CONTAINER...]
```

* --all , -a :显示所有的容器，包括未运行的。
* --format :指定返回值的模板文件。
* --no-stream :展示当前状态就直接退出了，不再实时更新。
* --no-trunc :不截断输出。

#### 3.3 docker exec

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

* -d :分离模式: 在后台运行
* -i :即使没有附加也保持STDIN 打开
* -t :分配一个伪终端

#### 3.4 docker inspect

```bash
#获取容器/镜像的元数据
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```

* -f :指定返回值的模板文件。
* -s :显示总的文件大小。
* --type :为指定类型返回JSON。

#### 3.5 docker logs

```bash
docker logs [OPTIONS] CONTAINER
```

* -f : 跟踪日志输出
* -t : 显示时间戳
* --tail :仅列出最新N条容器日志
* --since :显示某个开始时间的所有日志

#### 3.6 docker top

```bash
#查看容器中运行的进程信息
docker top [OPTIONS] CONTAINER [ps OPTIONS]
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
