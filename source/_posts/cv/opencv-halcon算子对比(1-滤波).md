---
title: opencv-halcon算子对比(1-滤波)
categories: ml
tags:
  - halcon
date: 2017-04-12 23:04:22
---

#### 滤波(filter)
>均值滤波
```c++
//halcon
 mean_image(image, imageMean, 3, 3) 

 //opencv
 // Point(-1,-1)表示锚点在中心
 blur(image, imageMean, Size(3,3),Point(-1,-1))
```
>中值滤波
```c++
//halcon
//circle:核的形状 mirrored:边界像素点处理方式
median_image(image, imageMedian, 'circle', 3, 'mirrored')

 //opencv
medianBlur( image, imageMedian, 3)
```
<!--more-->
>高斯滤波
```c++
//halcon
gauss_filter(image, imageGauss, 5) 

 //opencv
//第4和5参数表示核函数在X Y方向的标准偏差
GaussianBlur(image, imageGauss, Size(5, 5), 0, 0 )

```
>双边滤波
```

 //opencv
bilateralFilter( image, out, 25, 25*2, 25/2 );
```
#### 形态学(morphology)

>膨胀
(dilate [daɪˈlet])   
```c++
//halcon
dilation_circle (region, regionDilation, 100)
dilation_rectangle1 (region, regionDilation, 40, 40)

//opencv
//获取自定义核 
Mat element = getStructuringElement(MORPH_RECT, Size(15, 15));  
Mat out;
//进行膨胀操作
dilate(image, out, element);  
```

>腐蚀
(erosion [ɪ'roʒən])  
```c++
//halcon
erosion_circle (region, regionErosion, 10)
erosion_rectangle1 (region, regionErosion, 10, 10)

//opencv
//获取自定义核  
Mat element = getStructuringElement(MORPH_RECT, Size(15, 15));  
Mat out;  
//进行腐蚀操作  
erode(image,out, element);  
```

>开运算(opening)     
用来消除小物体、在纤细点处分离物体、平滑较大物体的边界的同时并不明显改变其面积.
```c++
//halcon
gen_circle (StructElement, 200, 200, 100.5)
opening (Region, StructElement, RegionClosing)
opening_circle (Region, RegionOpening, 3.5)
opening_rectangle1 (Region, RegionOpening, 10, 10)

>闭运算(closing)   
用于排除小型黑洞(黑色区域)
```c++
//halcon
gen_circle (StructElement, 200, 200, 100.5)
closing (Region, StructElement, RegionClosing)
closing_circle (Region, RegionClosing, 3.5)
closing_rectangle1 (Region, RegionClosing, 10, 10)
```