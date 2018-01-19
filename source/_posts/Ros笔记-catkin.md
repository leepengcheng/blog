---
title: Ros笔记-catkin
categories: robot
tags:
  - ros
date: 2017-03-20 21:18:28
---

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