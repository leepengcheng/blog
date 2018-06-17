---
title: docker 常用命令
categories: others
tags:
  - docker
date: 2018-06-15 16:45:52
---


最近开始学spark/docker和tensorflow，发现程序员界对微软大法深深的恶意。    
全特么的要Linux，但是工作电脑都是windows，所以只能上docker toolbox了。  
docker toolbox其实就是在windows里跑了linux(boot2docker)虚拟机，然后在linux虚拟机   
里跑docker。所以要把windows上写的东西部署到docker上需要挂载两次。

#### docker run
>docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```cmd
-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；

-d: 后台运行容器，并返回容器ID；

-i: 以交互模式运行容器，通常与 -t 同时使用；

-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

--name="nginx-lb": 为容器指定一个名称(可通过该名称对容器进行操作)

--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；

--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；

-h "mars": 指定容器的hostname；

-e username="ritchie": 设置环境变量；

--env-file=[]: 从指定文件读入环境变量；

--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；

-m :设置容器使用内存最大值；

--net="bridge": 指定容器的网络连接类型(bridge/host/none/container)

--link=[]: 添加链接到另一个容器；

--expose=[]: 开放一个端口或一组端口；
```
实例：
```bash
#使用镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx。
docker run --name mynginx -d nginx:latest

# 使用镜像nginx:latest以后台模式启动一个容器,并将容器的80端口映射到主机随机端口
docker run -P -d nginx:latest

# 使用镜像nginx:latest以后台模式启动一个容器,将容器的80端口映射到主机的80端口,主机的目录/data映射到容器的/data。
docker run -d -p 80:80 -v /data:/data  nginx:latest

# 使用镜像nginx:latest以交互模式启动一个容器,在容器内执行/bin/bash命令
docker run -it nginx:latest /bin/bash
```

* 4.此时E:\mydocker已经挂载到了虚拟机的/data文件夹下。  
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

#### docker start/stop/restart
>docker start [OPTIONS] CONTAINER [CONTAINER...]
>docker stop [OPTIONS] CONTAINER [CONTAINER...]
>docker restart [OPTIONS] CONTAINER [CONTAINER...]
```bash
#启动已被停止的容器
$ docker start myrunoob
#停止运行中的容器
$ docker start myrunoob
#重启容器
$ docker start myrunoob
```

* docker ps
>docker ps [OPTIONS]
```bash
-a :显示所有的容器，包括未运行的。

-f :根据条件过滤显示的内容。

--format :指定返回值的模板文件。

-l :显示最近创建的容器。

-n :列出最近创建的n个容器。

--no-trunc :不截断输出。

-q :静默模式，只显示容器编号。

-s :显示总的文件大小。
```

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

### docker rm
>docker rm [OPTIONS] CONTAINER [CONTAINER...] 

**OPTIONS**
```bash
-f :通过SIGKILL信号强制删除一个运行中的容器

-l :移除容器间的网络连接，而非容器本身

-v :删除与容器关联的卷
```

**EXAMPLE**
```bash
# 强制删除容器db01、db02
docker rm -f db01 db02

# 移除容器nginx01对容器db01的连接，连接名db
docker rm -l db 

删除容器nginx01,并删除容器挂载的数据卷
docker rm -v nginx01
```


#### docker pause/unpause：暂停/恢复容器的进程
>docker pause [OPTIONS] CONTAINER [CONTAINER...]
**EXAMPLE**
```bash
# 暂停/恢复数据库容器db01提供服务
docker pause    db01
docker unpause  db01
```

### docker create:创建新的容器
>docker create [OPTIONS] IMAGE [COMMAND] [ARG...]

**OPTIONS**
```bash
# 参见docker run
```
**EXAMPLE**
```bash
# 参见docker run
```


### docker exec :在容器中执行命令
>docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

**OPTIONS**
```bash
-d :分离模式: 在后台运行

-i :即使没有附加也保持STDIN 打开

-t :分配一个伪终端
```
**EXAMPLE**
```bash
# 在容器mynginx中以交互模式执行容器内/root/runoob.sh脚本
docker exec -it mynginx /bin/sh /root/runoob.sh

在容器mynginx中开启一个交互模式的终端
docker exec -it  mynginx /bin/bash
```


#### docker inspect : 获取容器/镜像的元数据
>docker inspect [OPTIONS] NAME|ID [NAME|ID...]
**OPTIONS**
```bash
-f :指定返回值的模板文件(golang模板)

-s :显示总的文件大小。

--type :为指定类型返回JSON。
```
**EXAMPLE**
```bash
#docker jq管道(仅linux下)
docker inspect ros  | jq -r ‘.[0].NetworkSettings.IPAddress’ 

#获取ros容器的入口脚本(在windos下需要双引号)
#with 表示作用域
docker inspect -f '{{with .Config}} IP :{{.Entrypoint}} {{end}}'  ros


#获取最近创建的容器中Id不为空的名称和退出码
#.Name表示容器名称
docker inspect -f '{{if ne "" .Id }}{{.Name}} {{.State.ExitCode}}{{ end }}' $(docker ps -aq)

#获取ros容器.Config.Env的第3个值(数组类型)
docker inspect -f '{{index .Config.Env 2}}' ros



#获取ros容器.Config.Env的第3个值
#适用于array类型
docker inspect -f '{{index .Config.Env  2}}' ros

#等价于docker inspect -f '{{.Config.Hostname}}' ros
#适用于map类型
docker inspect -f '{{index .Config  "Hostname"}}' ros

#返回json格式
docker  inspect -f "{{json .Config}}" ros


```



### docker top :查看容器中运行的进程
>docker top [OPTIONS] CONTAINER [ps OPTIONS]

**OPTIONS**
```bash
-d :分离模式: 在后台运行

-i :即使没有附加也保持STDIN 打开

-t :分配一个伪终端
```
**EXAMPLE**
```bash
# 在容器mynginx中以交互模式执行容器内/root/runoob.sh脚本
docker exec -it mynginx /bin/sh /root/runoob.sh

在容器mynginx中开启一个交互模式的终端
docker exec -it  mynginx /bin/bash
```
