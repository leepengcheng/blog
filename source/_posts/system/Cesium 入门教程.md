---
title: Cesium 使用教程
categories: others
tags:
  - cesium
  - javascript
date: 2018-03-15 18:15:52
---

* 设置控件的显示
```javascript
var viewer = new Cesium.Viewer('cesiumContainer', {
    homeButton:false, //是否显示home控件
    navigationHelpButton:false, //是否显示帮助信息控件
    animation:false, //是否显示动画控件
    timeline:false, //是否显示时间线控件
    sceneModePicker:false, //是否显示投影方式控件
    geocoder:false, //是否显示地名查找控件
    baseLayerPicker:false,//是否显示图层选择控件
    fullscreenButton:true,//是否显示全屏显示控件
    selectionIndicator : true,//选择是显示方框的选择控件
    infoBox : true //点击对象后显示模板的信息控件
});true
```

* 隐藏底部版本信息
```javascript
viewer._cesiumWidget._creditContainer.style.display="none";
```