---
title: C++多线程之thread
categories: programming
tags:
  - c++
  - 多线程
date: 2017-09-25 00:07:40
---


#### 安装配置postgresql

* 安装postgresql
```bash
sudo apt-get update & sudo apt-get install postgresql
```
* 修改密码
```pysql
sudo -u postgres psql
#> ALTER USER postgres WITH PASSWORD 'postgres' #修改默认账户postgres的密码
#> CREATE USER kong; CREATE DATABASE kong OWNER kong; #创建kong的账户/数据库
#> ALTER USER kong  WITH PASSWORD 'kong';  #修改默认账户kong的密码
```