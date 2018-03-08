---
title: ROS包-service和actionlib
categories: robot
tags:
  - ros
date: 2017-03-20 21:18:28
---

 
####  文件结构
```yaml
--catkin_ws
  --.catkin_tools #catkin init 生成的命名空间文件夹
  --build         #编译生成的文件，不包执行文件
  --devel         #生成的执行文件以及配置文件夹
    --include     #srv/action/msg生成的头文件夹
    --lib
      --pkg1     #对应包编译生成的库和可执行文件夹
  --logs          #日志文件夹
  --src           #源代码文件夹
    --pkg1        #pkg1包文件夹
      --action    #自定义action文件夹
      --launch    #启动文件夹
      --msg       #自定义msg文件夹
      --srv       #自定义srv文件夹
      --include   #节点的头文件
      --src       #主程序
      --CMakeLists.txt  #pkg1的cmake文件
      --package.xml     #pkg1的包描述文件(供catkin使用)
```

####   编写CMakeLists.txt
```python

# 添加依赖包
find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
 actionlib            #actionlib包
  actionlib_msgs
  message_generation  #生成消息
)




############################################################
#自定义msg消息,添加msg/demo_msg.msg
#@会在devel/include下生成demo_msg.文件,对应结构体为pkg1::demo_msg
#####消息结构体#####
# string greeting
# int32  number
############
 add_message_files(
   FILES
   demo_msg.msg
 )


#自定义srv消息,添加srv/demo_msg.srv
#@会在devel/include下生成demo_srvRequest.h/demo_srvResponse.h/demo_srv.h
#@demo_srv.h包含结构体demo_srv,成员结构体Request（demo_srvRequest)和Response(demo_srvResponse)
####服务输入/输出####
# string in #输入
# ---
# string out#输出
#############
 add_service_files(
   FILES
   demo_srv.srv
 )


#自定义action消息，添加srv/Demo_action.action
#@会在devel/include下生成Demo_action(|Action)(Goal|Feedback|Result).h 以及Demo_actionAction.h共7个头文件
#@Demo_actionAction.h包含结构体Demo_actionAction,包含Demo_actionGoal/Demo_actionFeedback/Demo_actionResult,需要在使用时实现该结构体
#####action目标/结果/反馈######
# int32 count       #目标
# ---
# int32 final_count   #结果
# ---
# int32 current_number #反馈
######################
  add_action_files(
   FILES
   Demo_action.action
 )
###############################################


#将srv/msg/action转换为.h文件
 generate_messages(
   DEPENDENCIES
   std_msgs
   actionlib_msgs
 )

 catkin_package(
  CATKIN_DEPENDS roscpp rospy std_msgs actionlib actionlib_msgs message_runtime 
)

#？？？？
add_dependencies(demo_topic_publisher pkg1_generate_messages_cpp)
```

####  修改package.xml
```xml
  <build_depend>roscpp</build_depend>
  <build_depend>rospy</build_depend>
  <build_depend>std_msgs</build_depend>
  <!-- 生成srv/action/msg需要 -->
  <build_depend>message_generation</build_depend>
  <!-- 自定义actionlib需要 -->
  <build_depend>actionlib</build_depend>
  <build_depend>actionlib_msgs</build_depend>

  <run_depend>roscpp</run_depend>
  <run_depend>rospy</run_depend>
  <run_depend>std_msgs</run_depend>
  <run_depend>actionlib</run_depend>
  <run_depend>actionlib_msgs</run_depend>
  <run_depend>message_runtime</run_depend>

```



#### 自定义Service

```c++
//服务端：创建并处理服务
#include "ros/ros.h"
#include "pkg1/demo_srv.h"
#include <iostream>
#include <sstream>
//形式：bool function(srv::Request&,srv::Response&)
bool demo_service_callback(pkg1::demo_srv::Request  &req,pkg1::demo_srv::Response &res)
{
 ROS_INFO("From Client  [%s], Server says [%s]",req.in.c_str(),ss.c_str());
  std::stringstream ss;
  ss << "Received  Here";
  res.out = ss.str();
  ROS_INFO("From Client  [%s], Server says [%s]",req.in.c_str(),res.out.c_str());
  return true;
}
int main(int argc, char **argv)
{ 
  ros::init(argc, argv, "demo_service_server");
  ros::NodeHandle n; //demo_srv结构：输入:(in:string)| 输出:(out:string)
  ros::ServiceServer service = n.advertiseService("demo_service", demo_service_callback);//发布服务
  ROS_INFO("Ready to receive from client.");
  ros::spin();
  return 0;
}
// ########################
//客户端：调用服务
#include "ros/ros.h"
#include "std_msgs/Int32.h"
#include <iostream>
#include "pkg1/demo_srv.h"
#include <iostream>
#include <sstream>
using namespace std;

int main(int argc, char **argv)
{
  ros::init(argc, argv, "demo_service_client");
  ros::NodeHandle n;
  ros::Rate loop_rate(10);
  ros::ServiceClient client = n.serviceClient<pkg1::demo_srv>("demo_service");
	while (ros::ok())
	{
	  pkg1::demo_srv srv;//创建srv消息结构体
	  std::stringstream ss;
	  ss << "Sending from Here";
	  srv.request.in = ss.str();
    //调用服务的方法:client.call(srv),返回值位于srv.response中，调用成功call函数返回true
	  if (client.call(srv)) 
	  {
	    ROS_INFO("From Client  [%s], Server says [%s]",srv.request.in.c_str(),srv.response.out.c_str());
	  }
	  else
	  {
	    ROS_ERROR("Failed to call service");
	    return 1;
	  }
	ros::spinOnce();
	loop_rate.sleep();
	}
  return 0;
}

```
