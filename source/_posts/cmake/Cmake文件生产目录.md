---
title: Cmake常用命令及VScode的配置
categories: others
tags:
  - others
date: 2018-01-24 14:41:03
---

* SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/../bin) 

上面的语句能设置可执行文件的输出目录

在Win + VS环境下,会自动在你所设置的目录后面扩展一层 <CONFIG> 目录,所以最终生成的Debug版本程序会在 ${PROJECT_SOURCE_DIR}/../bin/Debug 目录下,Release版本程序会在 ${PROJECT_SOURCE_DIR}/../bin/Release 目录下.

在Linux + GCC环境下,无论是Debug还是Release,生成的可执行程序会直接放在你所设置的目录下,不会有差异.

 

*  SET(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/../lib) 

上面的语句能设置库文件的输出目录

在Win + VS环境下,会自动在你所设置的目录后面扩展一层 <CONFIG> 目录,所以最终生成的Debug版本库会在 ${PROJECT_SOURCE_DIR}/../lib/Debug 目录下,Release版本库会在 ${PROJECT_SOURCE_DIR}/../lib/Release 目录下.

在Linux + GCC环境下,无论是Debug还是Release,生成的库文件会直接放在你所设置的目录下,不会有差异.

 

*  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../bin)    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../bin) 

上面两条语句分别设置了Debug版本和Release版本可执行文件的输出目录,

一旦设置上面的属性,在任何环境下生成的可执行文件都将直接放在你所设置的目录.

 

*  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_DEBUG ${PROJECT_SOURCE_DIR}/../lib)    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${PROJECT_SOURCE_DIR}/../lib) 

上面两条语句分别设置了Debug版本和Release版本库文件的输出目录,

一旦设置上面的属性,在任何环境下生成的库文件都将直接放在你所设置的目录.

 

* set(CMAKE_DEBUG_POSTFIX "_d")    set(CMAKE_RELEASE_POSTFIX "_r") 

上面两条语句分别设置了Debug版本和Release版本下库文件的后缀名.

 

* set_target_properties(${TARGET_NAME} PROPERTIES DEBUG_POSTFIX "_d")     set_target_properties(${TARGET_NAME} PROPERTIES RELEASE_POSTFIX "_r") 

上面两条语句分别设置了Debug版本和Release版本下可执行文件的后缀名.


####  ARCHIVE|LIBRARY|RUNTIME|FRAMEWORK|BUNDLE
TARGETS格式的install命令规定了安装工程中的目标（targets）的规则。   
有5中可以被安装的目标文件：ARCHIVE，LIBRARY，RUNTIME，   FRAMEWORK，和BUNDLE。   
除了被标记为MACOSX_BUNDLE属性的可执行文件被当做OS X上的BUNDLE目标外，   
其他的可执行文件都被当做RUNTIME目标。静态链接的库文件总是被当做ARCHIVE目标。   
模块库总是被当做LIBRARY目标。对于动态库不是DLL格式的平台来说，   
动态库会被当做LIBRARY目标来对待，被标记为FRAMEWORK的动态库是例外，   
它们被当做OS X上的FRAMEWORK目标。对于DLL平台而言，   
动态库的DLL部分被当做一个RUNTIME目标而对应的导出库被当做是一个ARCHIVE目标。   
所有基于Windows的系统，包括Cygwin，都是DLL平台。ARCHIVE，LIBRARY，RUNTIME和   
FRAMEWORK参数改变了后续属性会加诸之上的目标的类型。如果只给出了一种类型，    
那么只有那种类型的目标会被安装（这样通常只会安装一个DLL或者一个导出库。）


