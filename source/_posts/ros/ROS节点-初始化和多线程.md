---
title: ROS节点-节点初始化
categories: robot
tags:
  - ros
date: 2017-03-20 21:18:28
---

#### 节点初始化
* `ros::init`简单的读取命令行参数和环境找出这样的节点名称、命名空间和重映射
* `ros::init`没有连接到master主机，这需要在初始化后，再利用ros::master::check()或其他函数来检查主机状态

* 当第一个`ros::NodeHandle`创建时候，会调用`ros::start()`
* 最后一个`ros::NodeHandle`销毁时，会调用 `ros::shutdown()`
* 如果想自己管理节点的生命期，需要开始时调用`ros::start()`，在关闭程序前调用`ros::shutdown()`

 ```
//第4个参数为：ros::init_options枚举类型，包括
//@NoSigintHandler：不安装SIGINT句柄.这种情况下你需要自己安装SIGINT句柄来保证节点在退出时候会正确的关闭
    SIGINT的默认操作是Ctrl+C终结一个进行，所以如果你想自己处理 SIGTERM 你需要使用跟这个选项.
//@AnonymousName:在节点名后面加随机数，保证节点名唯一
//@NoRosout:不向/rosout广播话题
ros::init(argc, argv, "nodeName");


//@ns:节点的命名空间，~表示私有节点,附加在nodeName后面
ros::NodeHandle nh("~");

//@topic_name:主题名称
//@callback_queue:队列大小
//@latch:为True时会保存队列中的最后一条消息进行发送(即使没有pub)
ros::Publisher pub=nh.advertise<std_msgs::String>("msg",10,true);
```

#### 主题名称的规则

* 当发布的主题名称中包含斜杠，如`/msg`,则覆盖节点名和命名空间名，最终的主题名为`/msg`
* 当发布的主题名称中没有斜杠，如 `msg`,无命名空间，最终的主题名为`/nodeName/msg`
* 当发布的主题名称中没有斜杠，如 `msg`,命名空间为`"~"`，最终的主题名也为`/nodeName/msg`
* 当发布的主题名称中没有斜杠，如 `msg`，命名空间为`"~/ns"`,，最终的主题名也为`/nodeName/ns/msg`
* 当发布的主题名称中没有斜杠，如 `msg`，命名空间为`"ns"`,，最终的主题名也为`/ns/msg`



#### 关闭节点
* 当调用`ros::shutdown()`函数来关闭节点，会终结所有开放的订阅，发布，服务，调用
* 默认的roscpp也会安装SIGINT句柄用来检测Ctrl-C，并自动为你关闭节点
* 检测状态采使用`ros::ok()`
* 也可以使用`ros::isShuttingDown()`，当`ros::shutdown()`调用时，该函数就会返回true,一般不鼓励使用，不过在一些高级用法中还是有用的。      
例如在持续运行的服务节点的回调函数，当调用`ros::shutdown()`的时候，回调函数应该立即关闭。这时候`ros::isShuttingDown()`就要用到，    
而 `ros::ok()` 就不能工作，因为主要回调函数在运行中，节点就不能关闭。
