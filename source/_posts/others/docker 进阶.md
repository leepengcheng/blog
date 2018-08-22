---
title: docker教程
categories: others
tags:
  - docker
date: 2017-02-08 12:18:23
---


### doker文件复制
```bash
命令:  docker cp [OPTIONS] CONTAINER_ID:SRC_PATH DEST_PATH|-
        docker cp [OPTIONS] SRC_PATH|- CONTAINER_ID:DEST_PATH
#docker cp <容器id>:<容器文件(目录)地址>     宿主机地址 
# 容器ID可通过:docker ps -a 查看
#复制容器文件/文件夹到宿主机
docker cp 60aba9b3a285:/tmp/flogo-web/build  /home/zen
#复制宿主机文件/文件夹到容器
docker cp   /home/zen 60aba9b3a285:/tmp/flogo-web/build
```


### linux免sudo登录
```bash
$ sudo groupadd docker
$ sudo gpasswd -a $USER docker
$ sudo service docker restart 
```

### nginx容器重启
* 方法1:直接重启容器
* 方法2:docker exec <container_id|container_name> nginx -s reload


### 容器dns解析
docker容器内部的dns ip永远为`127.0.0.11`;例如nginx需要设置`resolver 127.0.0.11;`
