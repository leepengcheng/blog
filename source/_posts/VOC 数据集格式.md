---
title: VOC数据集格式
categories: ml 
tags:
  - voc
date: 2017-01-02 15:59:19
---
#### JEPGImages
包含所有的图片数据文件,包含训练和测试图片

#### Annotations
包含`JEPGImages`文件夹中每张图片的xml描述

```xml
<annotation>

  <!-- 父文件夹名 -->
	<folder>VOC2007</folder> 
  <!-- 文件名 -->
	<filename>000001.jpg</filename>
  
	<source><!-- source:文件来源,可以无视 -->
		<database>The VOC2007 Database</database>
		<annotation>PASCAL VOC2007</annotation>
		<image>flickr</image>
		<flickrid>341012865</flickrid>
	</source>

	<owner>
		<flickrid>Fried Camels</flickrid>
		<name>Jinky the Fruit Bat</name>
	</owner>
  
	<size> <!-- 图像尺寸:宽高和通道数 -->
		<width>353</width>
		<height>500</height>
		<depth>3</depth>
	</size>
  
	<segmented>0</segmented> <!-- 是否可以用于分割 -->

	<object>
		<name>dog</name> <!-- 物体类别 -->
		<pose>Left</pose> <!-- 拍摄角度 -->
		<truncated>1</truncated> <!-- 是否被截断:0表示完整的图 -->
		<difficult>0</difficult> <!-- 是否难以识别:0表示容易 -->
		<bndbox>             <!-- 物体的boundingbox位置 -->
			<xmin>48</xmin>
			<ymin>240</ymin>
			<xmax>195</xmax>
			<ymax>371</ymax>
		</bndbox>
	</object>
	<object>
		<name>person</name>
		<pose>Left</pose>
		<truncated>1</truncated>
		<difficult>0</difficult>
		<bndbox>
			<xmin>8</xmin>
			<ymin>12</ymin>
			<xmax>352</xmax>
			<ymax>498</ymax>
		</bndbox>
	</object>
</annotation>

```



#### ImageSets
