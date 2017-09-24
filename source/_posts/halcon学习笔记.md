---
title: halcon学习笔记
categories: ml
tags:
  - halcon
date: 2017-04-12 23:04:22
---

#### 1.筛选出不规则的的网格
>核心算子:
* gauss_filter:
* crop_rectangle1:
* watersheds:
* select_shape:
```
*读取图片
read_image (Image, 'atoms')
*获得图片尺寸
get_image_size (Image, Width, Height)
*裁剪图片(高度/2)
crop_rectangle1 (Image, Image, Height / 2, 0, Height - 1, Width - 1)  
*获得裁剪后图片尺寸
get_image_size (Image, Width, Height)
*高斯过滤-卷积核大小5x5
gauss_filter (Image, ImageGauss, 5)
*分水岭算法：抽取网格
watersheds (ImageGauss, Basins, Watersheds)
* 选择非边界的完整网格
smallest_rectangle1 (Basins, Row1, Column1, Row2, Column2)
select_shape (Basins, SelectedRegions1, 'column1', 'and', 2, Width - 1)
select_shape (SelectedRegions1, SelectedRegions2, 'row1', 'and', 2, Height - 1)
select_shape (SelectedRegions2, SelectedRegions3, 'column2', 'and', 1, Width - 3)
select_shape (SelectedRegions3, Inner, 'row2', 'and', 1, Height - 3)
*选择不规则的网格->根据图像的几何矩
select_shape (Inner, Irregular, ['moments_i1','moments_i1'], 'or', [0,9.5e8], [1.5e8,1e10])
```
<!--more-->
#### 2.分类不同颜色的熔断器
>核心算子:
* gauss_filter:
* crop_rectangle1:
* watersheds:
* select_shape:
```
*设置熔断器的颜色
FuseColors := ['Orange','Red','Blue','Yellow','Green']
*熔断器对应的额定电流类型
FuseTypes := [5,10,15,20,30]
* 色相区间: Orange 10-30, Red 0-10...
HueRanges := [10,30,0,10,125,162,30,64,96,128]
Count := 0
dev_close_window ()
dev_open_window (0, 0, 800, 600, 'black', WH)
while (Count <= 4)
    *读取图片
    read_image (Image, 'color/color_fuses_0' + Count)
    * 设置text位置 显示当前图片名称
    set_tposition (WH, 12, 512)
    write_string (WH, 'color/color_fuses0' + Count + '.png')
    * ****
    * step: extract saturated hues
    * 将图片分别成RGB
    decompose3 (Image, Red, Green, Blue)
    * 将RGB转换为HSV：色相 饱和度 亮度
    trans_from_rgb (Red, Green, Blue, Hue, Saturation, Intensity, 'hsv')
    * 阈值分割:饱和度->分割出熔断器的头部区域
    threshold (Saturation, Saturated, 60, 255)
    * 从
    reduce_domain (Hue, Saturated, HueSaturated)
```