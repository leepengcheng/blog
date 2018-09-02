---
title: Protobuf使用
date: 2017-01-14 11:12:39
categories: others
tags:
  - protobuf
---

### Protobuf简介




### protobuf升级
系统当前的protoc是2.6，想升级为3
*  1.下载[protoc](https://github.com/google/protobuf/releases)并解压：

* 2.复制文件到本地
```bash
sudo cp bin/protoc /usr/bin/protoc
```

* 3.确认是否安装成功，在终端输入：
```bash
protoc  --version
```
### python版

* 安装grpcio和grpcio-tools
```bash
pip install grpcio
python -m pip install grpcio-tools googleapis-common-protos
```

### 生成文件
```bash
 python -m grpc_tools.protoc -I../../protos --python_out=. --grpc_python_out=. ../../protos/helloworld.proto
```

### 继承service,并实现rpc方法