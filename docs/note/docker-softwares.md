---
title: "Docker中安装各种软件"
date: 2020-06-12T09:10:40+08:00
draft: false
tags: 
 - "docker"
categories: 
 - "记"
toc: true
reward: false
mathjax: true
---

在CentOS下安装各种软件镜像。假设已经安装完毕docker环境。

> 安装方式:查看Docker[官方文档](https://docs.docker.com/engine/install/centos/)

<!--more-->


#### MySQL
拉取[官方镜像](https://hub.docker.com/_/mysql)

```shell
docker pull mysql:5.7   # 拉取 mysql 5.7
docker pull mysql       # 拉取最新版mysql镜像
```


- 如果建立目录映射


```shell
sudo docker run -p 3306:3306 --name mysql \
-v /usr/data/docker/mysql/conf:/etc/mysql \
-v /usr/data/docker/mysql/logs:/var/log/mysql \
-v /usr/data/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=docker_mysql \
-d mysql:8.0.20
```
- 默认不建立目录映射


```shell
sudo docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.20
```

- –name：容器名，此处命名为`mysql`

- -e：配置信息，此处配置mysql的root用户的登陆密码
- -p：端口映射，此处映射 主机3306端口 到 容器的3306端口
- -v：主机和容器的目录映射关系，":"前为主机目录，之后为容器目录

#### Redis

拉取[官方镜像](https://hub.docker.com/_/redis)

```shell
docker pull redis:5.0.9
```

```shell
docker run -d --name redis -p 6379:6379 \
-v /usr/local/docker/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /usr/local/docker/redis/data:/data \
redis:5.0.9 redis-server /etc/redis/redis.conf
```


<u>continue</u>