---
title: emqttd 笔记
date: 2017-01-14 11:12:39
categories: others
tags:
  - mqtt
---

#### 端口设置
需要注意的是WS访问地址是localhost:8083/mqtt
```yaml
- "18083:18083" #Dashboard 管理控制台端口
- "8080:8080"  #HTTP API 端口
- "1883:1883"   #MQTT 协议端口
- "8883:8883"   #MQTT-SSL 端口
- "8083:8083"  #MQTT-WebSocket 端口
- "8084:8084"  #MQTT-WebSocket-SSL端口
```
