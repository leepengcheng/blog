---
title: orb-slam2安装教程
date: 2017-01-10 15:12:39
categories: robot
tags:
  - slam
---


# 安装C++11
orb-slam2使用了C++11的线程和时间控制函数。 
```bash
$ sudo apt-get install gcc g++  
```
# 安装Pangolin
orb-slam2使用 Pangolin 构建可视化用户界面  
```bash
$ sudo apt-get install libglew-dev   #安装Glew  
$ sudo apt-get install cmake         #安装CMake  
$ sudo apt-get install libboost-dev libboost-thread-dev libboost-filesystem-dev  #安装Boost
$ sudo apt-get install libpython2.7-dev  #安装Python2 
$ git clone https://github.com/stevenlovegrove/Pangolin.git   #下载Pangolin
$ cd Pangolin  
$ mkdir build  
$ cd build  
$ cmake -DCPP11_NO_BOOST=1 ..  
$ make  
$ sudo make install
```
<!--more-->
# 安装OpenCV
```bash
$ sudo apt-get install build-essential libgtk2.0-dev libvtk5-dev libjpeg-dev 
libtiff4-dev libjasper-dev libopenexr-dev libtbb-dev #依赖项
$ mkdir build
$ cd build
$ cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
$ make -j10   #开10线程编译
$ sudo make install #安装
```

# 安装Eigen
``` bash
$ sudo apt-get install libeigen3-dev  
```

# 安装DBoW2和g2o
  orb-slam2使用修改版的DBoW2库进行回环检测，使用 g2o 库进行非线性优化.  
  这两个修改版的库被放在Thirdparty文件夹内，可手动编译或者用项目内置的build.sh进行编译(参见下面的`编译orb-slam2`)。 
  
# 编译orb-slam2
```
$ git clone https://github.com/raulmur/orb-slam2.git orb-slam2  
$ cd orb-slam2  
$ chmod +x build.sh  
$ ./build.sh  
```
生成的liborb-slam2.so位于lib目录下，可执行程序mono_tum, mono_kitti, rgbd_tum, stereo_kitti, mono_euroc and stereo_euroc位于Examples目录下。
#  运行例子
## Monocular单目实例
1)TUM数据集
从[TUM网站](http://vision.in.tum.de/data/datasets/rgbd-dataset/download)下载并解压一个序列，如：rgbd_dataset_freiburg1_desk2.tgz，执行下面的命令。  
(把TUMX.yaml 修改为TUM1.yaml <对应`freiburg1序列`>,TUM2.yaml <对应`freiburg2序列`> or TUM3.yaml <对应`freiburg3序列`>)  
```bash
$ ./Examples/Monocular/mono_tum Vocabulary/ORBvoc.txt ./Examples/Monocular/TUMX.yaml PATH_TO_SEQUENCE_FOLDER   
#for example
$ ./Examples/Monocular/mono_tum Vocabulary/ORBvoc.txt ./Examples/Monocular/TUM1.yaml ../tum_data/rgbd_dataset_freiburg1_desk2/
```
2)KITTI数据集
从[CVlibs网站]( http://www.cvlibs.net/datasets/kitti/eval_odometry.php)下载数据集(灰度图)， 把KITTIX.yaml  修改为 KITTI00-02.yaml, KITTI03.yaml or KITTI04-12.yaml，这些*xx.yaml各自对应于序列 0 to 2, 3, and 4 to 12.将PATH_TO_DATASET_FOLDER设为数据序列的解压目录.将SEQUENCE_NUMBER设为00~11： 
```bash
$ ./Examples/Monocular/mono_kitti Vocabulary/ORBvoc.txt Examples/Monocular/KITTIX.yaml PATH_TO_DATASET_FOLDER/dataset/sequences/SEQUENCE_NUMBER
```

## Stereo多目实例
1)KITTI数据集
从[CVlibs网站]( http://www.cvlibs.net/datasets/kitti/eval_odometry.php)下载数据集(灰度图)，把KITTIX.yaml  修改为 KITTI00-02.yaml, KITTI03.yaml or KITTI04-12.yaml，这些*xx.yaml各自对应于序列 0 to 2, 3, and 4 to 12.将PATH_TO_DATASET_FOLDER设为数据序列的解压目录.将SEQUENCE_NUMBER设为00~11：
```bash
$ ./Examples/Stereo/stereo_kitti Vocabulary/ORBvoc.txt Examples/Stereo/KITTIX.yaml PATH_TO_DATASET_FOLDER/dataset/sequences/SEQUENCE_NUMBER
```


## RGB-D实例
1)TUM 数据集
从[TUM网站](http://vision.in.tum.de/data/datasets/rgbd-dataset/download)下载并解压一个序列，如rgbd_dataset_freiburg1_desk2.tgz,运行RGB-D实例时需要RGBD(depth)图像和RGB图像
所以需要把每一张RGB图像与之对应的RGBD图像建立关联(在Examples/RGB-D/associations/目录下有一部分关联文件，可以直接使用),关联Python文件associate.py(根据timestamp进行关联)。       
```bash
#高博的slam-book代码中有associate.py文件
$ python associate.py PATH_TO_SEQUENCE/rgb.txt PATH_TO_SEQUENCE/depth.txt > associations.txt  
```

执行下面的命令,把 ASSOCIATIONS_FILE修改为对应的关联文件位置
```bash
$./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/TUMX.yaml PATH_TO_SEQUENCE_FOLDER ASSOCIATIONS_FILE  
# for example  
$./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt ./Examples/RGB-D/TUM1.yaml  ../tum-data/rgbd_dataset_freiburg1_desk2/ ./Examples/RGB-D/associations/fr1_desk2.txt   
```

# SLAM和Localization模式
你可在GUI中切换至SLAM模式或Localization模式。
* SLAM 模式
    这是默认模式. 此模式下，系统有三个线程并行运行: Tracking, Local Mapping and Loop Closing。 
    系统不停地定位相机，构建新的地图，并且试图闭合环形路径。
* Localization 模式
    当你有一个好的工作区域地图时，你可以使用此模式。在这种模式下，局部地图构建以及环路闭合检测将不工作。 
    系统在你提供的地图上定位相机 (此地图不再更新), 在必要时使用重定位（relocalization）。
