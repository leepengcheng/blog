---
title: Qt Android的asset文件
categories: programming
tags:
  - android
  - Qt
date: 2018-02-07 15:27:13
---

由于对java无爱,几经波折选择了qt作为业余开发app的框架,坑确实很多,但是确实很爽也很简单.    
昨天用Webview加载静态html时遇到了一点问题,由于webview的url必须是`file:///xxx/yyy.html`,    
所以不能将html和js写入qrc中然后用`qrc:/xxx.html`读取.最后尝试了2种方案:   


#### 方案1:
>将静态html保存在qrc中,app启动的时候读出来保存在指定路径,然后使用该指定路径的文件
实例代码如下:
```c++
QString initLocation()
{
    //app的临时路径
    QString tempLoc=QStandardPaths::writableLocation(QStandardPaths::TempLocation);
  //  QString loc2=QStandardPaths::writableLocation(QStandardPaths::AppDataLocation);
  //  QString loc3=QStandardPaths::writableLocation(QStandardPaths::ApplicationsLocation);
  //  QString loc4=QStandardPaths::writableLocation(QStandardPaths::CacheLocation);
  //  QString loc5=QStandardPaths::writableLocation(QStandardPaths::ConfigLocation);
  //  QString loc6=QStandardPaths::writableLocation(QStandardPaths::AppConfigLocation);
  //  QString loc7=QStandardPaths::writableLocation(QStandardPaths::DesktopLocation);
  //  QString loc8=QStandardPaths::writableLocation(QStandardPaths::DataLocation);
  //  QString loc=QGuiApplication::applicationDirPath();

    QDirIterator it(":/android/assets",QDirIterator::Subdirectories);
    QString source,target;
    while(it.hasNext())
    {
        source=it.next();
        QFileInfo f(source);
        if(f.isFile())
        {
            target=tempLoc+"/"+f.baseName();
            QFile::remove(target);
            QFile::copy(source,target);
        }
    }

    return tempLoc;

}
```
调用方法:webview.url=tempLoc+"/xxx.html"
* 优点:简单直观,可以远程读取html然后保存在本地
* 缺点:每次启动的时候需要把整个qrc读入内存,会降低启动速度/增大内存占用




#### 方案2:
>在目录下建立android/assets目录,然后将html静态文件保存在该目录下
```yaml
QAndroid_project:
 - my_project.pro
 - main.cpp
 - android
    - AndroidManifest.xml
    - assets
      -index.html
      -jquery.js
```
调用方法:`webview.url="file:///android_asset/index.html"`
* 优点:和原生安卓一样,简单方便,启动时不会加载,内存占用较少
* 缺点:资源文件打包在apk内部,无法在线更新