---
title: ROS启动-rosrun和roslaunch
categories: robot
tags:
  - ros
date: 2017-03-20 21:18:28
---

 
#### rosrun
```bash
$ rosrun  pkgname nodename _param:=1  __name:=testnode1  
# 普通参数,需要加下划线_,例如_param
# 特殊关键字,需要加双下划线,例如__name
#特殊关键字包括:__name __log __ip __hostname __master __ns
```