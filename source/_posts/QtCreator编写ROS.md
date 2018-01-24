---
title: QtCreator编写ROS
categories: ros
tags:
  - ros
date: 2018-01-24 15:51:52
---

### 1.准备工作
首先需要分清`catkin_make`的`catkin build`区别,后者属于`catkintools`编译,    
本文中的编译都需要使用`catkintools`,如果是`catkin_make`编译的,可以查看[这里](http://catkin-tools.readthedocs.io/en/latest/migration.html "catkintools")如何从`catkin_make`迁移到`catkintools`.    
如果未安装`catkin-tools`,需要先安装:
```bash
$ sudo apt-get update
$ sudo apt-get install python-catkin-tools
```

### 2.创建工作空间

```bash
$ mkdir ~/catkin_ws/src -p #创建目录
$ cd ~/catkin_ws
$ catkin init   #会在工作空间建立.catkin_tools文件夹
$ sudo cp mypkg    ~/catkin_ws/src/mypkg #复制包
$ catkin create pkg  mypkg   --catkin-deps  roscpp std_msgs  --system-deps Boost #或者直接创建包
```
创建包和节点还有1种方法就是使用`RoboWare`创建/编辑/删除包和节点,非常方便.但是`RoboWare`要求工作区必须包含`CMakeLists.txt`,     
此时可以创建软连接到src目录,之后就可以用`RoboWare`打开工作空间了,但是切记不要用它编译:
```bash
$ sudo  ln -s /opt/ros/kinetic/share/catkin/cmake/toplevel.cmake   ~/catkin_ws/src/CMakeLists.txt
```

### 3.配置QtCreator
* 打开 工具>构建和运行>选择构建套件
* 设置 Cmake generator:CodeBlocks -- Unix Makfiles
* 设置 CMake Configuration:只包含`QT_QMAKE_EXECUTABLE:STRING=%{Qt:qmakeExecutable}`



### 4.编译工作空间
```bash
# 删除默认编译器,使用QT Creator设置的编译器
* 在工作空间执行(非必须)`catkin config --cmake-args -DCMAKE_CXX_COMPILER:STRING=/usr/bin/g++ --`
# 设置工作目录(生成的二进制文件目录),也可以等生成后在Qt creator中改
* 在工作空间执行 `catkin config -DCATKIN_DEVEL_PREFIX=../devel -DCMAKE_INSTALL_PREFIX=../install`
* 在工作空间执行 `catkin build`(编译所有的包)或者`catkin build xxx`编译指定的包

```
### 5.打开QtCreator
* 在shell中打开QtCreator,例如`./qtcreator`,不要直接点击桌面图标启动,即使加了`bash  -i -c`依然无法编译成功(浪费了我2天时间)
* 选择**指定包**下的`CMakeLists.txt`打开,指定工作目录为"../build/pkgname",pkgname为选的包的名称


### 6.调试

在包的CMakeLists.txt中添加`set(CMAKE_BUILD_TYPE debug)`,然后打断点即可调试