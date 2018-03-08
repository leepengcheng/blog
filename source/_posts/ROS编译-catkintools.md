---
title: ROS编译-catkintools
categories: robot
tags:
  - ros
date: 2017-03-20 21:18:28
---
ros有3套编译工具:`cmake`,`catkin_make`,`catkin build`   
后2个其实就是`cmake`的封装,其中`catkin build`是 **catkintools** 里的命令.  
`catkintolls`是`catkin_make`的升级版,除了增加了一些功能,最大的不同是工作空间   
不需要创建顶层的`CMakelists.txt`,而是创建`.catkin_tools`文件夹.`catkin build`类似于   
`catkin_make_isolated`,会单独编译每个包,防止两个包有相同的节点.
```bash
# catkinbuild:常用工具
$ catkin_init_workspace  #生成指向top-level的CMakeLists(切记在src下执行)
$ catkin_make          #编译所有的包(在工作空间执行)

######################################################################

# catkintolls:常用工具
$ catkin init           #初始化工作空间,创建.catkin_tools文件夹(在工作空间执行)
$ catkin build          #编译所有的包(在工作空间执行),后面可以输入包名单独编译某个包
$ catkin clean          #清除创建的build和devel文件夹
$ catkin list           #列出当前工作空间的包,额外参数--deps:包依赖
$ catkin create pkg    #创建包,生成package.xml和CMakeLists.txt,额外参数(在src目录下)
# --catkin-deps:ros依赖包,例如如roscpp,等价于find_package()
# --system-deps:系统依赖包,如Boost,等价于find_package()
$ catkin config   #
#额外参数:--whitelist/--no-whitelist:设置需要编译的包/清除白名单
# --blacklis/--no-blacklist:设置不需要编译的包/清除黑名单
# --cmake-args:设置Cmake编译参数,例如-DCMAKE_CXX_COMPILER:STRING=/usr/bin/g++ --
```



#### catkin_make报错
>9月24日补充:最好使用Ubuntu14.04自带的python版本，否则还会引起其他蛋疼的问题，需要什么包单独安就行。

在ubuntu装完anaconda后再安装ros和catkin,使用catkin_make时候总是报错,折腾了一晚上无果，蛋疼无比。报错如下: 
```bash
catkin_pkg.package.InvalidPackage: Invalid package manifest "/opt/ros/kinetic/share/catkin/cmake/../package.xml": 
Error(s) in /opt/ros/kinetic/share/catkin/cmake/../package.xml:
- The manifest must not contain the following tags: depend, build_export_depend, buildtool_export_depend
CMake Error at /opt/ros/kinetic/share/catkin/cmake/safe_execute_process.cmake:11 (message):
  execute_process(/home/alex/anaconda3/envs/python2/bin/python
  "/opt/ros/kinetic/share/catkin/cmake/parse_package_xml.py"
  "/opt/ros/kinetic/share/catkin/cmake/../package.xml"
  "/home/alex/catkin_ws/build/catkin/catkin_generated/version/package.cmake")
  returned error code 1
```
时隔几天逛stackoverflow忽然发现某老司机有解决办法了，原来是anaconda的conda安装的ros和catkin的版本太低了。因此需要移除它们然后重新用pip安装。
```bash
pip install -U rosdep rosinstall_generator wstool rosinstall six vcstools #移除旧版本

conda install setuptools  #安装setuptools(如果报错)

rosdep update  #更新rosdep，如果还是报错，则需要用pip重新安装catking_pkg
```


* 安装`catkin_tools`
```bash
$ sudo apt-get update
$ sudo apt-get install python-catkin-tools
```