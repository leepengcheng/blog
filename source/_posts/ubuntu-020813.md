---
title: ubuntu 环境搭建
categories: others
tags:
  - ubuntu
date: 2017-02-08 12:18:23
---

### 安装opencv
所用版本为3.1
* 安装依赖项
```bash
$ sudo apt-get install build-essential libgtk2.0-dev libvtk5-dev libjpeg-dev libtiff4-dev libjasper-dev libopenexr-dev  libtbb-dev
```
* 编译
```bash
$ cd ~/opencv-3.1.0
$ mkdir build
$ cd build
$ cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
$ make -j4 # runs 4 jobs in parallel
$ sudo make install
```

### 