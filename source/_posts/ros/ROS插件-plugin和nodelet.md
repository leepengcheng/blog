---
title: ROS插件-plugin和nodelet
categories: robot
tags:
  - ros
date: 2017-03-20 21:18:28
---

 
### Ros  plugin

#### 定义基类
```c++
//#######################
// calculator_base.h
//######################

#ifndef PLUGINLIB_CALCULATOR_CALCULTOR_BASE_H_
#define PLUGINLIB_CALCULATOR_CALCULTOR_BASE_H_

namespace calculator_base 
{
  class calc_functions
  {
    public:
    
      //=0为纯虚函数,特点:1-没有函数体,2-告诉编译器为虚函数 3-声明语句,后面有分号
      //相当于c#/java中的abstract抽象函数
      virtual void get_numbers(double number1, double number2) = 0;
      virtual double operation() = 0;
      virtual ~calc_functions(){}

    protected:
      calc_functions(){}
  };
};
#endif
```
#### 继承基类并实现接口
```c++
//#######################
// calculator_plugins.h
//######################
#ifndef PLUGINLIB_CALCULATOR_CALCULTOR_PLUGINS_H_
#define PLUGINLIB_CALCULATOR_CALCULTOR_PLUGINS_H_
// 引用基类头文件
#include <pluginlib_calculator/calculator_base.h>
#include <cmath>

namespace calculator_plugins 
{
  //继承自基类
  class Add : public calculator_base::calc_functions
  {
    public:
      Add()
      {
        number1_ = 0;
        number2_ = 0;
      }

      //实现基类虚函数get_numbers
      void get_numbers(double number1, double number2)
      {
        try{
          number1_ = number1;
          number2_ = number2;
          }

        catch(int e)
        {
        std::cerr<<"Exception while inputting numbers"<<std::endl;
        }

      }	

      //实现基类operation()
      double operation()
      {
        return(number1_+number2_);
      }

    private:
      double number1_;
      double number2_;
  };

  //减法实现
  class Sub : public calculator_base::calc_functions
  // ...
  };

  //乘法实现
  class Mul : public calculator_base::calc_functions
  {
  // ...
  };
};
#endif
```

#### 导出插件
```c++
//#######################
// calculator_plugins.cpp
//######################
#include <pluginlib/class_list_macros.h>  //插件宏定义
#include <pluginlib_calculator/calculator_base.h>  //基类定义
#include <pluginlib_calculator/calculator_plugins.h> //继承类定义与实现

//PLUGINLIB_EXPORT_CLASS(class_type, base_class_type)
PLUGINLIB_EXPORT_CLASS(calculator_plugins::Add, calculator_base::calc_functions);
PLUGINLIB_EXPORT_CLASS(calculator_plugins::Sub, calculator_base::calc_functions);
PLUGINLIB_EXPORT_CLASS(calculator_plugins::Mul, calculator_base::calc_functions);
PLUGINLIB_EXPORT_CLASS(calculator_plugins::Div, calculator_base::calc_functions);
```
#### 编译插件
```c++
//#######################
// CMakeLists.txt
//######################
//
add_library(pluginlib_calculator src/calculator_plugins.cpp)
target_link_libraries(pluginlib_calculator ${catkin_LIBRARIES})
```

#### 编写插件描述plugin.xml
```xml
<!-- //#######################
// calculator_plugins.xml
//###################### -->


<!-- <class_libraries>该标签可包含多个库 -->
<!-- 库位置:devel/lib/libpluginlib_calculator.so -->
<library path="lib/libpluginlib_calculator">

  <!-- name:插件工程名/类名  type:继承类的命令空间/继承类名  base_class_type:基类的命令空间/基类名 -->
  <class name="pluginlib_calculator/Add" type="calculator_plugins::Add" base_class_type="calculator_base::calc_functions">
    <!-- 描述说明 -->
    <description>This is a add plugin.</description>
  </class>

  <class name="pluginlib_calculator/Sub" type="calculator_plugins::Sub" base_class_type="calculator_base::calc_functions">
    <description>This is a sub plugin.</description>
  </class>

  <class name="pluginlib_calculator/Mul" type="calculator_plugins::Mul" base_class_type="calculator_base::calc_functions">
    <description>This is a mul plugin.</description>
  </class>

  <class name="pluginlib_calculator/Div" type="calculator_plugins::Div" base_class_type="calculator_base::calc_functions">
    <description>This is a div plugin.</description>
  </class>

</library>
<!-- </class_libraries> -->
```
#### 在package.xml中注册插件
```xml
<!-- package.xml -->
  <export>
    <!-- pluginlib_calculator:!!!!定义基类的package名称!!!  plugin:插件描述文件calculator_plugins.xml -->
    <pluginlib_calculator plugin="${prefix}/calculator_plugins.xml" />
    <!-- 如果插件类和基类不在同一package,需要添加对插件基类所在package的依赖 -->
    <!-- <build_depend>calculator_base</build_depend> -->
    <!-- <run_depend>calculator_base</run_depend> -->
  </export>
```

#### 列出插件
```sh
rospack  plugins --attrib=plugin pluginlib_calculator
#>> pluginlib_calculator ~/catkin_ws/src/pluginlib_calculator/calculator_plugins.xml
```



#### 调用插件
```c++

#include <boost/shared_ptr.hpp>
//pluginlib加载对应库的函数
#include <pluginlib/class_loader.h>
//加载对应的基础类的头文件
#include <pluginlib_calculator/calculator_base.h>

int main(int argc, char** argv)
{
  //ClassLoader(std::string package, std::string base_class,
  //std::string attrib_name = std::string("plugin"),
  //std::vector<std::string> plugin_xml_paths = std::vector<std::string>());
  pluginlib::ClassLoader<calculator_base::calc_functions> calc_loader("pluginlib_calculator", "calculator_base::calc_functions");
  try
  {
    boost::shared_ptr<calculator_base::calc_functions> add = calc_loader.createInstance("pluginlib_calculator/Add");
    add->get_numbers(10.0,10.0);
    double result = add->operation();

    ROS_INFO("Triangle area: %.2f", result);
  }
  catch(pluginlib::PluginlibException& ex)
  {
    ROS_ERROR("The plugin failed to load for some reason. Error: %s", ex.what());
  }
}

```
----------

### Nodelet

#### 定义类继承Nodelet
步骤如下
* 添加对应的include头文件
* 去除main()函数
* 继承nodelet::Nodelet 
* 在onInit()中初始化topic,不能做耗时任务,如果需要循环pub,需要用多线程
* 添加PLUGINLIB_DECLARE_CLASS宏定义  
```c++
    //宏定义头文件
    #include <pluginlib/class_list_macros.h>  
    //nodelet 头文件
    #include <nodelet/nodelet.h>  
    #include <ros/ros.h>  
    #include <std_msgs/Float64.h>  
    #include <stdio.h>  
    #include <math.h> //fabs  
    namespace nodelet_ns  
    {  
      
    class Plus : public nodelet::Nodelet  
    {  
    public:  
      Plus(): value_(0){}
    private:  
      //实现虚函数onInit()
      virtual void onInit()  
      {  
        //获取当前的node
        ros::NodeHandle& private_nh = getPrivateNodeHandle();  
        //获取参数:value
        private_nh.getParam("value", value_);  
        //定义pub
        pub = private_nh.advertise<std_msgs::Float64>("out", 10); 
        //定义sub 
        sub = private_nh.subscribe("in", 10, &Plus::callback, this);  
      }  
      
      void callback(const std_msgs::Float64::ConstPtr& input)  
      {  
        //!!!!shared_ptr定义指针
        std_msgs::Float64Ptr output(new std_msgs::Float64());  
        output->data = input->data + value_;  
        NODELET_DEBUG("Adding %f to get %f", value_, output->data);
        //发送指针
        pub.publish(output);  
      }  
      
      ros::Publisher pub;  
      ros::Subscriber sub;  
      double value_;  
    };  

    //PLUGINLIB_DECLARE_CLASS(pkg, class_name, class_type, base_class_type)
    PLUGINLIB_DECLARE_CLASS(nodelet_ns, Plus, nodelet_ns::Plus, nodelet::Nodelet); 
    }  
```


#### 编写插件描述nodelet.xml
```xml
<!-- 库位置:devel/lib/libnodelet_math.so -->
<library path="lib/libnodelet_math">
  <!-- name:插件工程名/类名  type:继承类的命令空间/继承类名  base_class_type:基类的命令空间/基类名 -->
  <class name="nodelet_tutorial_math/Plus" type="nodelet_tutorial_math::Plus" base_class_type="nodelet::Nodelet">
    <description> 
      A node to add a value and republish.
    </description>
  </class>
</library>
```

#### 在package.xml中注册插件
```xml
<build_depend>nodelet</build_depend>
<run_depend>nodelet</run_depend>

<export>
  <nodelet plugin="${prefix}/nodelet.xml" />
</export>
```


####  使用nodelet
launch.xml如下所示：
* Plus的输出`/Plus/out` 直接指向Plus的输入`Plus2/in`
* Plus3的输出`/Plus3/in` 直接指向Plus3的输入`Plus2/out`
```xml
<launch>
    <node pkg="nodelet" type="nodelet" name="standalone_nodelet"  args="manager" output="screen"/>

    <node pkg="nodelet" type="nodelet" name="Plus" args="load nodelet_tutorial_math/Plus standalone_nodelet" output="screen">
        <remap from="/Plus/out" to="Plus2/in"/>
    </node>          

    <!-- 参数:value: 10 -->
    <!-- <rosparam param="Plus2" file="$(find nodelet_tutorial_math)/plus_default.yaml"/> -->
    <node pkg="nodelet" type="nodelet" name="Plus2" args="load nodelet_tutorial_math/Plus standalone_nodelet" output="screen">
        <rosparam file="$(find nodelet_tutorial_math)/plus_default.yaml"/>
    </node>

    <node pkg="nodelet" type="nodelet" name="Plus3" args="standalone nodelet_tutorial_math/Plus" output="screen">
        <param name="value" type="double" value="2.5"/>
        <remap from="Plus3/in" to="Plus2/out"/>
    </node>
</launch>

<!-- 输入输出图如下所示 -->
<!-- 10       ---( +0 )---    10   ---( +10 )---    20    --(+2.5 )-- 22.5     -->
<!-- /Plus/in ---(Plus)---/Plus2/in---(Plus2)---/Plus2/out--(Plus3)--Plus3/out -->
```



#### nodelet的运行参数
注意 standalone 和 manager的区别，standalone个人理解用于测试单个节点
```bash
nodelet manager               - Launch a nodelet manager node.  启动一个manager
nodelet load pkg/Type manager - Launch a nodelet of type pkg/Type on manager manager. 向manager中加载nodelet
nodelet standalone pkg/Type   - Launch a nodelet of type pkg/Type in a standalone node. 无需创建manager
nodelet unload name manager   - Unload a nodelet by name from manager. 从manager中移除nodelet
num_worker_threads            - 线程池线程个数
```