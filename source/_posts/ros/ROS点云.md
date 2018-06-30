---
title: ROS点云
categories: robot
tags:
  - ros
  - pcl
date: 2018-03-20 21:18:28
---

#### sensor_msgs::PointCloud
```c++
#include <ros/ros.h>
#include <sensor_msgs/PointCloud.h>

sensor_msgs::PointCloud cloud;
cloud.header.stamp = ros::Time::now();
cloud.header.frame_id = "world";
cloud.points.resize(num_points);

cloud.channels.resize(1);
cloud.channels[0].name = "rgb";
cloud.channels[0].values.resize(num_points);

for(unsigned int i = 0; i < num_points; ++i){
  cloud.points[i].x = 0.1*i;
  cloud.points[i].y = 0.1*i;
  cloud.points[i].z = 5;
  cloud.channels[0].values[i] = 255;
}
////转换为sensor_msgs::PointCloud2
// sensor_msgs::PointCloud2 rosCloud2;
// sensor_msgs::convertPointCloudToPointCloud2(cloud,rosCloud2);
```


#### pcl::PointCloud<T>
```c++
typedef pcl::PointXYZ PointT;
typedef pcl::PointCloud<PointT> PCLPointCloud;

PCLPointCloud::Ptr pclCloud(new PCLPointCloud);
pclPointCloud->is_dense = true;
PointT p;
for (int i = 0; i < num_points; i++) {
    if (vertices[i].z) {
        p.x = vertices[i].x;
        p.y = vertices[i].y;
        p.z = vertices[i].z;
        pclCloud->points.push_back(p);
    }

}

////转换为sensor_msgs::PointCloud2
// sensor_msgs::PointCloud2 rosCloud2;
// pcl::toROSMsg(pclCloud, rosCloud2);
```



#### sensor_msgs::PointCloud2

#### pcl_ros

* pcd_to_pointcloud
>pcd点云文件转sensor_msgs::Pointcloud2

输入/参数|类型|描述
- | :-: | -: 
cloud_pcd|(sensor_msgs/PointCloud2)|发布的点云topic名称(无法改变)
~frame_id|(str, default: /base_link)|生成的点云的frame_id

```bash
# 发布cloud_file.pcd为点云 频率10HZ fram_id=/odom
rosrun  pcl_ros pcd_to_pointcloud cloud_file.pcd 0.1 _frame_id:=/odom
rosrun  tf static_transform_publisher  0 0 0.5 -1.57 0 -1.57 "world" "odom" 10
rosrun  rviz rviz
```


* pointcloud_to_pcd  
>sensor_msgs::Pointcloud2转pcd点云文件

输入/参数|类型|描述
- | :-: | -: 
input |(sensor_msgs/PointCloud2)|输入的点云topic名称    
~prefix |(str)|创建的pcd文件的前缀    
~fixed_frame |(str)|输出fixed_frame转换关系到pcd的VIEWPOINT属性中    
~binary |(bool, default: false)|输出二进制pcd文件   
~compressed|(bool, default: false)|输出二进制压缩pcd文件    
```bash
# 会生成类似于/home/zen/pcd_1530239076566253.pcd文件
rosrun pcl_ros pointcloud_to_pcd input:=/cloud_pcd _prefix:=/home/zen/pcd_
# 查看pcd文件
pcl_viewer /home/zen/pcd_1530239076566253.pcd
```



#### pcl消息与ros消息转换
数据类型|描述
- | :-: |
`sensor_msgs::PointCloud2`|常用的ros点云数据结构，推荐使用
`sensor_msgs::PointCloud`|旧版的ros点云数据结构(逐步废弃)
`pcl::PointCloud<T>`|常用的pcl点云结构,推荐使用
`pcl::PCLPointCloud2`|pcl点云的ros接口

------

命名空间|消息1|消息2
- | :- | :-:|
`pcl_conversions`|`pcl::PCLPointCloud`|`sensor_msgs::PointCloud2`
`pcl`|`pcl::PointCloud<T>`|`sensor_msgs::PointCloud2`
`sensor_msgs`|`sensor_msgs::PointCloud`|`sensor_msgs::PointCloud2`
```c++

//命名空间:sensor_msgs
//sensor_msgs::PointCloud 与 sensor_msgs::PointCloud2
sensor_msgs::convertPointCloudToPointCloud2 (const sensor_msgs::PointCloud &input, sensor_msgs::PointCloud2 &output)
sensor_msgs::convertPointCloud2ToPointCloud (const sensor_msgs::PointCloud2 &input, sensor_msgs::PointCloud &output)

//命名空间:pcl
// pcl::PointCloud<T>  与  sensor_msgs::PointCloud2
pcl::toROSMsg(const pcl::PointCloud<T> &pcl_cloud, sensor_msgs::PointCloud2 &cloud)
pcl::fromROSMsg(const sensor_msgs::PointCloud2 &cloud, pcl::PointCloud<T> &pcl_cloud)
pcl::moveFromROSMsg(sensor_msgs::PointCloud2 &cloud, pcl::PointCloud<T> &pcl_cloud)


//命名空间:pcl_conversions
// pcl::PCLPointCloud2  与  sensor_msgs::PointCloud2
pcl_conversions::toPCL(const sensor_msgs::PointCloud2 &pc2, pcl::PCLPointCloud2 &pcl_pc2)
pcl_conversions::fromPCL(const pcl::PCLPointCloud2 &pcl_pc2, sensor_msgs::PointCloud2 &pc2)
pcl_conversions::moveToPCL(sensor_msgs::PointCloud2 &pc2, pcl::PCLPointCloud2 &pcl_pc2)
pcl_conversions::moveFromPCL(pcl::PCLPointCloud2 &pcl_pc2, sensor_msgs::PointCloud2 &pc2)

```