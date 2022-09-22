---
author: "li_mingxie"
title: "【docker笔记】docker_常用命令(2)"
date: 2022-08-01T23:28:49+08:00
tags: [
    "docker",
]
categories: [
    "docker"
]
---

## 1.volume，mount

```bash
docker run -it -v /test:/soft centos /bin/bash
```
<!--more-->  
```bash
#创建一个volume
$ docker volume create my-vol

#查看
$ docker volume ls

DRIVER              VOLUME NAME
local               my-vol

#删除
docker volume rm my-vol

#详情
$ docker volume inspect my-vol
[
    {
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]
```

加载一个 数据卷 到容器的 /usr/share/nginx/html 目录

```bash
$ docker run -d -P \
    --name web \
    # -v my-vol:/usr/share/nginx/html \
    --mount source=my-vol,target=/usr/share/nginx/html \
    nginx:alpine
```

常用方式

```bash
docker run --name $CONTAINER_NAME -it \
-v $PWD/$CONTAINER_NAME/app:/app:rw \
-v $PWD/$CONTAINER_NAME/data:/data:ro \
avocado-cloud:latest /bin/bash
```

## 2.dockerfile

* FROM- 镜像从那里来
* MAINTAINER- 镜像维护者信息
* RUN- 构建镜像执行的命令，每一次RUN都会构建一层
* CMD- 容器启动的命令，如果有多个则以最后一个为准，也可以为ENTRYPOINT提供参数
* ENTRYPOINT- 容器进入时执行的命令(他与CMD不同的是命令可以累加)
* VOLUME- 定义数据卷，如果没有定义则使用默认
* USER- 指定后续执行的用户组和用户
* WORKDIR- 切换当前执行的工作目录
* HEALTHCHECH- 健康检测指令
* ARG- 变量属性值，但不在容器内部起作用
* EXPOSE- 暴露端口
* ENV- 变量属性值，容器内部也会起作用
* ADD- 添加文件，如果是压缩文件也解压
* COPY- 添加文件，以复制的形式

## 3.docker build

```bash
docker build [OPTIONS] PATH | URL | -

docker build -f /path/dockerfile -t mysql:5.7 . 
```

* --build-arg=[] :设置镜像创建时的变量；
* --cpu-shares :设置 cpu 使用权重；
* --cpu-period :限制 CPU CFS周期；
* --cpu-quota :限制 CPU CFS配额；
* --cpuset-cpus :指定使用的CPU id；
* --cpuset-mems :指定使用的内存 id；
* --disable-content-trust :忽略校验，默认开启；
* -f :指定要使用的Dockerfile路径；
* --force-rm :设置镜像过程中删除中间容器；
* --isolation :使用容器隔离技术；
* --label=[] :设置镜像使用的元数据；
* -m :设置内存最大值；
* --memory-swap :设置Swap的最大值为内存+swap，"-1"表示不限swap；
* --no-cache :创建镜像的过程不使用缓存；
* --pull :尝试去更新镜像的新版本；
* --quiet, -q :安静模式，成功后只输出镜像 ID；
* --rm :设置镜像成功后删除中间容器；
* --shm-size :设置/dev/shm的大小，默认值是64M；
* --ulimit :Ulimit配置。
* --squash :将 Dockerfile 中所有的操作压缩为一层。
* --tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。
* --network: 默认 default。在构建期间设置RUN指令的网络模式

## 4.docker-compose

```bash
docker-compose up -d
```

docker-compose.yml

```yml
version: "3"
services:
  aa_dev:
    image: xxxxxx.com/servicename:${VERSION}
    restart: always
    privileged: true
    environment:
      - TZ=Asia/Shanghai
      - CRON_TIME=0 1 * * *
    volumes:
      - /project/aa_dev/job:/go/src/app/job:rw
    ports:
      - 20002:8002/tcp
```

----------------------------------------------

欢迎大家的意见和交流

`email: li_mingxie@163.com`
