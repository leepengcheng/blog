---
title: node-red 自定义节点
date: 2017-01-14 11:12:39
categories: others
tags:
  - iot
  - node-red
---



文件结构
```yaml
node-red-lower-case:
  - lower-case.html
  - lower-case.css
  - package.json
```


安装
```bash
#安装到本地/home/zen/.node-red/node_modules目录中(软链接)
#/home/zen/.node-red/node-red-lower-case是用户创建的
npm  install /home/zen/.node-red/node-red-lower-case
```