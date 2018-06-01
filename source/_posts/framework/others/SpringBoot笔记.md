---
title: SpringBoot笔记
categories: others
tags:
  - javaweb
date: 2017-03-16 11:12:09
---

#### 端口一直被占用
>Intellij IDEA启动工程时提示端口占用,尝试在`application.properties`中更改端口`server.port=8080`,   
试了N个端口依然显示被占用，后来发现只要关闭进程中的`java.exe`，重启IDEA即可。

<!--more-->
#### @RequestBody与用@ModelAttribute   
考虑下面的controller
```java
    @RequestMapping(value ="/update",method ={RequestMethod.GET,RequestMethod.POST})
    @ResponseBody          //@ModelAttribute
    public String modifyUser(@RequestBody User user) {
        userMapper.updateUser(user);
        return user.toString();
    }
```
---
```python
from requests import post,get
import json
#使用@RequestBody
headers={"Content-type":"application/json"} #必须设置此项
data={'age': 18, 'id': 1, 'name': 'wangerxiao'} #dict
json_str_data=json.dumps(data) ##json字符串,如果是ajax,使用`stringify`
post("http://localhost/update",data=json_str_data,headers=header).text#数据放到body中
##使用@ModelAttribute
get("http://localhost/update",params=data).text#数据放到url中
```

#### Ajax无返回值
>当服务端路由的返回值不是json对象时，如果ajax请求的`dataType`为`'json'`，则无法正确返回值  
通过chrome调试发现此时response其实是有值的,提示错误为`textStatus = "parsererror"`，即返回值  
格式解析错误，只需将`dataType`改为`'text'`即可正常显示
```javascript
$.ajax({
      type: "POST",
      url: "/login",
      data: {
          "name": name,
          "pwd": pwd
      },
      dataType: "json",
      success: function (response) {
          console.log(response)
      },
      error: function (XMLHttpRequest, textStatus, errorThrown) {
          console.log(textStatus)
      }
  });
```

#### Aop环绕通知返回值
>在用websocket的时候发现返回状态码是101,但是就是没有返回值。后来才发现环绕通知没有设置返回值。
```java
    @Around("UploadPointCut()")    //环绕通知
    public Object around(ProceedingJoinPoint proceedingJoinPoint) {
        Object result = null;
        try {
            String method=proceedingJoinPoint.getSignature().getName();
            System.out.println(method+":around start");
            result = proceedingJoinPoint.proceed();
            System.out.println(method+":around end");
        } catch (Throwable e) {
            System.out.println(e.getMessage());
        }
        return result;//必须要有返回值
    }
```