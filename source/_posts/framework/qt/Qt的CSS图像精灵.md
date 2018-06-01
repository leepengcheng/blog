---
title: Qt的CSS图像精灵
categories: programming
tags:
  - c++
  - Qt
date: 2017-02-07 15:27:13
---

有的时候需要给控件添加图标，如果每个控件图标都添加1个资源文件    
则很难管理,此时可以借鉴css 图像精灵的做法，把多个图标合成一张    
大图(雪碧图),然后定位。此时一般有2种做法：   
#### 利用QSS
如果图标大小是64x64,则下列函数选择第2行第3列(1+64/64,1+128/64)的图标。
```c++
Widget::setStyleSheet("border-image: url(:/sprite.png)  64 0 0 128  repeat repeat;")
```
<!--more-->

#### 利用QPixMap
核心函数就是`QPixmap::copy`,个人感觉该方法比QSS好用。  
```c++
QPixmap QPixmap::copy(int x, int y, int width, int height) const
```

#### 编码设置
>1.最有效的方法还是是QString::fromLocal8Bit()方法和用Qt语言家进行国际化。   
>2.编码设置,网上抄的。不过多数时候并没有什么卵用。 

```c++
//设置界面显示的编码
QTextCodec *codec=QTextCodec::codecForName("GBK");
QTextCodec::setCodecForLocale(codec);
//设置翻译成其他语言的格式
QTextCodec::setCodecForTr(QTextCodec::codecForLocale());
//QTextCodec::setCodecForTr(codec);
//设置读取源文件采取的编码方式
QTextCodec *codec1=QTextCodec::codecForName("UTF-8");
QTextCodec::setCodecForCStrings(codec1);
```