---
title: Cmake常用命令及VScode的配置
categories: others
tags:
  - others
date: 2018-01-24 14:41:03
---

### VScode调试C++的配置
* launch.json基本配置
```json
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

* VSCode预定义的变量
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

* 引用环境变量
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

* 引用VSCode配置参数:
```json
  "${config:Name}" //example: ${config:editor.fontSize}
  "${command:CommandID}" //example: ${command:explorer.newFolder}
```

### Cmake 常用命令
```cmake
link_libraries()
target_link_libraries()
add_compile_options(-std=c++11)
add_executable(main main.cpp)
```