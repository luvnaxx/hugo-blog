---
title: "MySQL8基本操作"
date: 2020-06-12T11:16:30+08:00
draft: false
tags: 
 - "mysql"
categories: 
 - "记"
toc: true
reward: false
mathjax: true
---

创建新用户并授权

> MySQL 8.0.20

<!--more-->

## 使用root用户登录mysql

```shell
mysql -uroot -p
```

## 创建新用户

```shell
// 低版本数据库
create user '用户名'@'%' identified by '密码';
// 高版本数据库
create user '用户名'@'%' identified with mysql_native_password by '密码';
```

> 低版本方式没有测试

#### 修改密码

1. 登录命令行

   ```shell
   mysql -uroot -p
   ```

2. 查看密码

   ```shell
   use mysql;
   select Host,User,authentication_string from user;
   ```

3. 修改密码

   ```shell
   ALTER user '用户名'@'localhost' IDENTIFIED BY 'mysql';
   ALTER user '用户名'@'%' IDENTIFIED BY 'mysql';
   ```


## 给该用户添加权限

```shell
// 指定数据库
grant all privileges on 指定的数据库.* to '用户名'@'%';
// 全部数据库
grant all privileges on *.* to '用户名'@'%';
```

