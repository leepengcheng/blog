---
title: Cmake常用命令及VScode的配置
categories: others
tags:
  - others
date: 2018-01-24 14:41:03
---

## VSCode配置

### C++开发配置
```json
// launch.json基本配置
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch CMake-built program",
            "type": "cppdbg",
            "request": "launch",
            // //需要cmake>3.72
            // "program": "${command:cmake.launchTargetProgramPath}",
            "program": "${workspaceFolder}/build/${fileBasenameNoExtension}",
            "stopAtEntry": true,
            "args":["${env:PATH}"],
            "cwd": "${workspaceRoot}"
        }
    ]
}
```

<!-- more -->

### 环境变量引用
```json
{
    "type": "node",
    "request": "launch",
    "name": "Launch Program",
    "program": "${workspaceFolder}/app.js",
    "cwd": "${workspaceFolder}",
    "args": [ "${env:PATH}" ]
}
```

### VSCode配置参数引用
```json
  "${config:Name}" //example: ${config:editor.fontSize}
  "${command:CommandID}" //example: ${command:explorer.newFolder}
```



###  VSCode预定义变量
```json
    "${workspaceFolder}" //the path of the folder opened in VS Code
    "${workspaceFolderBasename}" //the name of the folder opened in VS Code without any slashes (/)
    "${file}" //he current opened file
    "${relativeFile}" //the current opened file relative to workspaceFolder
    "${fileBasename}" //the current opened file's basename
    "${fileBasenameNoExtension}" //the current opened file's basename with no file extension
    "${fileDirname}" //the current opened file's dirname
    "${fileExtname}" //the current opened file's extension
    "${cwd}" //the task runner's current working directory on startup
    "${lineNumber}" //the current selected line number in the active file
```

-----------------------------------------------------------------------------
## Cmake 笔记
### 注意事项
>**由于CMake会从缓存中读取变量,所以更改CMakeLists.txt后需要删除CMakeCache.txt文件才能生效**      
>`include(FindOpencv)` 和`find_package(Opencv)`效果类似,但`find_package`有`module`和`config`模式

### 常用变量

* CMAKE_SOURCE_DIR:工程顶层目录
```cmake
PROJECT_SOURCE_DIR         #等价CMAKE_SOURCE_DIR
${PROJECTNAME}_SOURCE_DIR  #等价CMAKE_SOURCE_DIR
```
* CMAKE_BINARY_DIR:工程文件放置目录
```cmake
PROJECT_BINARY_DIR  #等价CMAKE_BINARY_DIR
${PROJECTNAME}_BINARY_DIR  #等价CMAKE_BINARY_DIR
```

* CMAKE_BUILD_TYPE:编译类型
```cmake
set(CMAKE_BUILD_TYPE "debug") #CMakelists.txt
cmake -DMAKE_BUILD_TYPE=DEBUF #cmake命令行
```
* CMAKE_MODULE_PATH:
>CMAKE定义模块的搜索路径
```cmake
list( APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules )
find_package( G2O REQUIRED )
find_package( CSparse REQUIRED )
```

* CMAKE_CURRENT_LIST_FILE:CMakelists.txt全路径
```cmake
CMAKE_CURRENT_SOURCE_DIR #CMakelists.txt所在目录
CMAKE_CURRENT_LIST_DIR   #CMakelists.txt所在目录
CMAKE_CURRENT_LIST_LINE  #CMakelists.txt的当前行数
```

* CMAKE_PREFIX_PATH:库/头文件的搜索目录
* CMAKE_INCLUDE_PATH:头文件包含目录
```cmake
#linux:${CMAKE_PREFIX_PATH}/(lib/<arch> | lib | share)/cmake/<name>*/   
#linux:${CMAKE_PREFIX_PATH}/(lib/<arch> | lib | share)/<name>*/
#linux:${CMAKE_PREFIX_PATH}/(lib/<arch> | lib | share)/<name>*/(cmake|CMake)/
find_package()  

#在${CMAKE_PREFIX_PATH}/bin/<arch>或者${CMAKE_PROGRAM_PATH}中搜索
find_program()  

#在${CMAKE_PREFIX_PATH}/lib/<arch>或者${CMAKE_LIBRARY_PATH}中搜索
find_library()  

#在${CMAKE_PREFIX_PATH}/include/<arch> 或者 ${CMAKE_INCLUDE_PATH}
find_file()/find_path()
```
* CMAKE_LIBRARY_ARCHITECTURE:系统架构
```cmake
message(${CMAKE_LIBRARY_ARCHITECTURE})  #x86_64-linux-gnu(ubuntu 16.04 x86_64)
```

### 常用命令
```cmake
link_libraries(mylib1 mylib2) #所有的add_executable都会链接mylib1和mylib2库
target_link_libraries(myexe mylib1) #myexe只会链接mylib1
add_executable(myexe main.cpp) #可执行程序

add_library(mylib SHARED|STATIC  add.cpp)
#当${BUILD_SHARED_LIBS}为TRUE时,下列表达式默认为SHARED
add_library(mylib  add.cpp)
#除非显示指定为STATIC
add_library(mylib  STATIC add.cpp)
```

* add_compile_options
>编译选项
```cmake
add_compile_options(-std=c++11) #使用c++11
```

* add_definitions
>添加变量定义,所有定义的属性都保存在工程目录(DIRECTORY)的COMPILE_DEFINITIONS属性中
```cmake
add_definitions(-DFOO -DBAR)
#get_directory_property(defs DIRECTORY ${CMAKE_SOURCE_DIR} COMPILE_DEFINITIONS)
get_directory_property(defs  COMPILE_DEFINITIONS)
foreach(def ${defs})
    message(">>>>>" ${def})  
endforeach()
#输出:FOO  BAR
```

* get_filename_component
>获取文件名的路径/后缀等信息
```cmake
get_filename_component(target "usr/local/hello.cpp" NAME_WE)
message(${target}) #输出hello
#NAME_WE    不包含路径和后缀
#NAME       不包含路径
#DIRECTORY  只包含路径
#EXT        只包含后缀
#ABSOLUTE   绝对路径
#REALPATH   实际路径(链接)
```


* file
>文件操作
```cmake
file(WRITE "test.txt" "helloworld")             #写出
file(READ "test.txt" data)                      #读入
file(MD5 ${PROJECT_SOURCE_DIR}/test.txt value)  #获得文件内容MD5/SHA1/SHA224/SHA256/SHA384/SHA512
file(RENAME "test.txt"  "test1.txt")            #重命名    
file(REMOVE "test.txt")                         #删除文件  
file(TIMESTAMP ${PROJECT_SOURCE_DIR}/test.txt  filestamp) #文件创建时间戳记
file(DOWNLOAD www.baidu.com/data.txt ./data.txt 5)          #下载文件
FILE(COPY ../../devel/lib/libcanprocess.so  DESTINATION ${CAN_PROJECT_DIR}/can/lib/) #注意后面的路径尾部加斜杠/
file(GLOB variable [RELATIVE path] [globbing expressions]...) #搜索文件
file(GLOB ADSLIB_SRC src/AdsLib/*.cpp src/AdsLib/*.h)
file(GLOB_RECURSE variable [RELATIVE path]

```


* excute_process
>执行外部命令
```cmake
# COMMAND:命令(bash -c "cmd string")
# WORKING_DIRECTORY:命令执行目录
# TIMEOUT:超时则停止,单位s
# OUTPUT_VARIABLE:输出的结果
# ERROR_VARIABLE:错误的结果
excute_process(COMMAND bash -c "ls -a"
               WORKING_DIRECTORY ${CMAKE_PROJECT_DIR}
               TIMEOUT  10
               OUTPUT_VARIABLE urdf_dir
               ERROR_VARIABLE  error)
```


* include
>引用并执行cmake文件,默认搜索目录为${CMAKE_MODULE_PATH}
```cmake

include(FindProtobuf)    #在/usr/share/cmake-3.5/Modules目录下搜索FindProtobuf.cmake
#protobuf_generate_cpp函数/宏在FindProtobuf.cmake中定义
protobuf_generate_cpp(PROTO_SRC PROTO_HEADER echo.proto) #生成protobuff的.cc和.h文件

```

* option
>定义选项的开关(ON/OFF),当更改值时,务必清除CMakeCache.txt和CMakeFiles.txt
```cmake
option(USE_NAME "YOUR NAME" OFF)
if(USE_NAME)
    message("##########")
else()
    message("*********")
endif()
```

* configure_file
>配置输入文件 到输出文件,主要作用是使得普通文件也可以使用cmake中的变量
>默认将config.h.in中的 ${VAR} 和@VAR@ 替换为cmake中的变量
>当使用@ONLY属性时,只替换@VAR@变量
```cpp
#cmakedefine  USE_FSTREAM
#cmakedefine  PI       ${PI}
```

```cmake
configure_file(config.h.in  config.h COPYONLY)  #等同于COPY,可以在QT Creator中显示config.h.in
configure_file(config.h.in  config.h)
option(USE_FSTREAM "USE_FSTREAM" ON)
set(PI 3.14159)


#输出:config.h
#define  USE_FSTREAM
#define  PI 3.14159
```


##系统平台判断
```cmake
${CMAKE_SYSTEM}   #Windows-10.0.16299
${CMAKE_SYSTEM_NAME}  #IF(CMAKE_SYSTEM_NAME MATCHES "Windows")
#或直接查看 WIN32|UNIX|APPLE是否定义
```
