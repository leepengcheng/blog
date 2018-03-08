---
title: ROS包-dynamic_reconfigure
categories: robot
tags:
  - ros
date: 2017-03-20 21:18:28
---

 
`dynamic_reconfigure`本质上就是Service


#### 创建cfg文件
```python
# cfg/chapter2.cfg

#!/usr/bin/env python
PACKAGE = "chapter2_tutorials"

from dynamic_reconfigure.parameter_generator_catkin import *

gen = ParameterGenerator()

gen.add("int_param",    int_t,    0, "An Integer parameter", 1,  0, 100)
gen.add("double_param", double_t, 0, "A double parameter",    .1, 0,   1)
gen.add("str_param",    str_t,    0, "A string parameter",  "Chapter2_dynamic_reconfigure")
gen.add("bool_param",   bool_t,   0, "A Boolean parameter",  True)

size_enum = gen.enum([ gen.const("Low",      int_t, 0, "Low is 0"),
                       gen.const("Medium",     int_t, 1, "Medium is 1"),
                       gen.const("High",      int_t, 2, "Hight is 2")],
                     "Select from the list")

gen.add("size", int_t, 0, "Select from the list", 1, 0, 3, edit_method=size_enum)

#########generate(pkgname, nodename, name)
exit(gen.generate(PACKAGE, "chapter2_tutorials", "chapter2_"))

```



####   编写CMakeLists.txt
```python

find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  message_generation
  dynamic_reconfigure #动态配置库
)

generate_dynamic_reconfigure_options(
  cfg/chapter2.cfg
)

###????
add_dependencies(chap2_example4 chapter2_tutorials_gencfg)
```



####  修改package.xml
```xml
  <build_depend>roscpp</build_depend>
  <build_depend>rospy</build_depend>
  <build_depend>std_msgs</build_depend>
  <!-- 生成srv/action/msg/cfg需要 -->
  <build_depend>message_generation</build_depend>
  <!-- 生成dynamic_reconfigure需要 -->
  <build_depend>dynamic_reconfigure</build_depend>

  <run_depend>roscpp</run_depend>
  <run_depend>rospy</run_depend>
  <run_depend>std_msgs</run_depend>
  <run_depend>dynamic_reconfigure</run_depend>
```



#### 配置dynamic_reconfigure
如果节点名称为`example4`，则dynamic_reconfigure会生成以下节点和任务:    
* /example4/parameter_descriptions `#topic:静态参数描述`
* /example4/parameter_updates`#topic:更新参数后发布`
* /example4/set_parameters`#service:设置参数的服务`
```c++
#include <ros/ros.h>
#include<std_msgs/String.h>
//包含的头文件
#include <dynamic_reconfigure/server.h>
//包含的Config定义-pkgname/cfgname_Config.h
#include <chapter2_tutorials/chapter2_Config.h>

int data=0;
void callback(chapter2_tutorials::chapter2_Config &config, uint32_t level) {
  data=config.int_param;
  ROS_INFO("Reconfigure Request: %d %f %s %s %d",
            config.int_param, config.double_param,
            config.str_param.c_str(),
            config.bool_param?"True":"False",
            config.size);
}

int main(int argc, char **argv) {
  ros::init(argc, argv, "example4");
  ros::NodeHandle nh("~");
  ros::Rate rate(5);
  ros::Publisher pub=nh.advertise<std_msgs::String>("msg",20);

  
  //创建dynamic_reconfigure::Server
  dynamic_reconfigure::Server<chapter2_tutorials::chapter2_Config> server;
  //创建dynamic_reconfigure::Server::CallbackType 回调函数
  dynamic_reconfigure::Server<chapter2_tutorials::chapter2_Config>::CallbackType f;
  f = boost::bind(&callback, _1, _2);
  server.setCallback(f);


  while (ros::ok())
  {
    std_msgs::String msg;
    std::stringstream ss;
    ss <<data;
    msg.data = ss.str();
    pub.publish(msg);
    ros::spinOnce();
    rate.sleep();
  }
  return 0;
}
```
