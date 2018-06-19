---
title: Cesium 使用教程
categories: others
tags:
  - cesium
  - javascript
date: 2018-03-15 18:15:52
---

* Hello World

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
  <title>Hello World!</title>
  <script src="../Build/Cesium/Cesium.js"></script>
  <style>
      @import url(../Build/Cesium/Widgets/widgets.css);
      html, body, #cesiumContainer {
          width: 100%; height: 100%; margin: 0; padding: 0; overflow: hidden;
      }
  </style>
</head>
<body>
  <div id="cesiumContainer"></div>
  <script>
      // * 设置控件的显示
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
      });
      // * 隐藏底部版本信息
      viewer._cesiumWidget._creditContainer.style.display="none";
  </script>
</body>
</html>
```

