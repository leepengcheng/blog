---
title: Win7下Docker文件共享
categories: others
tags:
  - docker
date: 2017-02-15 16:45:52
---


最近开始学spark/docker和tensorflow，发现程序员界对微软大法深深的恶意。    
全特么的要Linux，但是工作电脑都是windows，所以只能上docker toolbox了。  
docker toolbox其实就是在windows里跑了linux(boot2docker)虚拟机，然后在linux虚拟机   
里跑docker。所以要把windows上写的东西部署到docker上需要挂载两次。
<!--more-->
* 1.打开virtualbox下的共享文件夹,然后添加E:\mydocker,命名为workspace,勾选固定分配。  
假设 E:\mydocker文件夹里只有1个hello-word.txt

* 2.打开cmd,连接linux虚拟机，默认名称是default 

```bash
$ docker-machine ls  ##列出linux虚拟机
$ docker-machine ssh default ##连接linux虚拟机
```

* 3.在虚拟机内创建文件夹data
```bash
$ sudo -i ##获得root权限
$ mkdir /data ##在当前目录下创建/data文件夹
$ sudo mount -t vboxsf workspace /data #把E:/mydocker挂载到虚拟机的/data目录下
$ cd /data #进入/data文件夹
$ ls  #列出文件夹内的文件：hello-world.txt
$ cat hello-world.txt #显示文件内容
```
* 4.此时E:\mydocker已经挂载到了虚拟机的/data文件夹下。  
添加共享文件夹时不勾选自动挂载即可。 
然后新建卷的时候用-v挂载即可，即使重启windows也没事。    
新建容器  
```bash
$ sudo docker run -dit -v /data_share:/data_share spkinger/ubuntu_lnmp:v12 /bin/bash 
```
重启后即可
```bash
$ sudo docker start [容器名称] 
```

*  docker build
```bash
#使用当前目录的Dockerfile创建镜像
$ docker build -t nginx:v10086  . 
#镜像名称nginx:v10086,注意后面的 . 指当前目录
```

*  docker run
```bash
#创建一个新的容器并运行一个命令
$ docker run --name  myngix -d -it -p 81:80  nginx:v10086
$ docker run --name  mysql -d -it -e MYSQL_ROOT_PASSWORD=123456  -p 3306:3306 mysql:latest
#--name: 为容器指定一个名称
#-d:后台运行容器，并返回容器ID
#-i:以交互模式运行容器，通常与-t同时使用
#-t: 为容器重新分配一个伪输入终端，通常与-i同时使用
#-p:将容器的80端口映射到服务器的81端口
```
* docker exec
```bash
#在已经运行容器中以交互模式执行容器内/root/runoob.sh脚本
$ docker exec -it mynginx /bin/sh /root/runoob.sh
```

* docker start/stop/restart/rm
```bash
#启动已被停止的容器
$ docker start myrunoob
#停止运行中的容器
$ docker start myrunoob
#重启容器
$ docker start myrunoob
#强制删除容器
docker rm -f db01、db02
 
```

* docker ps
```bash
#列出所有在运行的容器
$ docker ps
#列出所有的容器
$ docker ps -a
##列出最近创建的n个容器
$ docker ps -n 5
#列出所有创建的容器ID
$ docker ps -a -q

```

>0910更新:建议升级win10,然后使用`Docker for Windows`，体验完爆`Docker ToolBox`