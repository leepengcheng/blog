---
title: Movieit 模块说明
categories: robot
tags:
  - ros
  - moveit
date: 2017-03-20 21:18:28
---
## moveit 系统结构如图所示
![moveit](../images/moveit.jpg  "Optional title attribute")




---
* The move_group node（move_group节点）
如上图，其中最重要的就是move_group节点，充当整合器：整合多个独立的组件,并提供ROS风格的Action和service  

* User Interface（用户接口，三种接口可供调用）      
    C++， 利用move_group_interface包可以方便使用move_group。      
    Python，利用moveit_commander包       
    GUI（界面），利用Motion Planning 的 Rviz插件。            

* 配置，move_group是ROS节点，它在ROS param server获取三种信息，       
    URDF ，从ROS param server中查找robot_description，获取URDF，它是机器人的描述文件。             
    SRDF ，从ROS param server中查找robot_description_semantic，获取SRDF.它一般通过MoveIt! Setup Assistant生成。    
    MoveIt! configuration ，从ROS param server中获取更多信息，如joint limits, kinematics, motion planning,   
    perception and other information。配置文件由MoveIt! Setup Assistant生成，存放在MoveIt! config目录中。  

* Robot Interface（机器人接口）     
    move_group通过ROS topics和actions与机器人通讯，获取机器人的状态（位置，节点等），  
    获取点云或其他传感器数据再传递给机器人的控制器。

* Joint State Information（节点状态信息）      
    move_group监听 /joint_states 主题确定状态信息。例如：确定每个节点的位置。  
    Move_group能够监听在这主题的多个发布器信息，即使是发布部分的信息（例如：独立的发布器可能是用于机械臂或移动机器人）。  
    ove_group不会建立自己的节点状态发布器。这就需要在每个机器人单独来建立。  

* Transform Information（变换信息）        
    Move_group通过ROS TF库来监视变换信息。这允许节点获取全局的姿态信息。  
    例如：navigation包发布机器人的map frame和base frame到TF，move_group可以使用TF找出这个变换信息，在内部使用。  
    注意：Move_group只是监听TF，你需要启动robot_state_publisher才能发布TF。

* Controller Interface （控制器接口）     
    通过ROS的action接口，FollowJointTrajectoryAction接口来使用控制器。  
    一个机器人的服务器服务于这个action-这个服务器不是有move_group提供。  
    move_group只会实例化客户端与机器人的控制器action服务器通讯。  

* Planning Scene（规划场景）          
    move_group使用规划场景监视器来维护规划场景。  
    场景是世界的和机器人的状态的表现。  
    机器人状态包含机器人刚性连接到机器人的所有物体。  
    关于维护和更新规划场景的体系结构的详细信息在下面的规划场景部分中描述。  

* Extensible Capabilities（可扩展能力）          
    move_group的结构被设计成容易扩展，独立的能力如抓放，运动学，运动规划。  
    扩展自公共类，但实际作为独立的插件运行。  
    插件可经由一系列的ROS yaml parameters 和ROS pluginlib库配置。
---