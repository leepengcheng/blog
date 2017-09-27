---
title: pugixml简易教程
categories: programming
tags:
  - c++
date: 2017-01-16 16:35:28
---

>C++的Xml解析库五花八门,比如QtXml tinyxml等,相比而还是觉得pugixml好用些。     
pugixml的api清晰简洁,速度也挺快,还支持Xpath,使用很方便。   
pugixml官网： [pugixml.org](http://pugixml.org/ "扑街XML")

#### 读取XML文件
```c++
using namespace pugi;
xml_document doc;
//读取字符串格式的xml
//xml_parse_result result=doc.load_string(xmlstring);
//if(result.status!=xml_parse_status::status_ok)
//{
//    return 0;
//}
//由于SCD文件采用的utf8格式的编码，所以装载的时候，需要设置一下编码方式
if (!doc.load_file(filePath,pugi::parse_default,pugi::encoding_utf8))
{
  return -1;
}
//获取根节点
xml_node root=doc.root();
//XPath对节点的查找，具体的xpath的规则，大伙可以自己去找相关的资料
xpath_node_set IED_list=doc.select_nodes("/SCL/IED");
for(xpath_node_set::const_iterator it=IED_list.begin();it!=IED_list.end();it++)
{
//遍历节点，获取属性值
xpath_node it_xpath=*IED_it;
QString name=it_xpath.node().attribute("name").value();
}
//对于节点text值的获取，对于值的获取可以根据实际情况进行选择，比如as_string(),as_int()等，这个也是很方便。
xml_node node;
QString text=node.text().as_string();####保存XML文件
```

<!--more-->


#### 保存XML文件
```c++
xml_document doc;
//增加说明
xml_node decl=doc.prepend_child(pugi::node_declaration);
decl.append_attribute("version")="1.0";
decl.append_attribute("encoding")="UTF-8";
//增加节点
xml_node root = doc.append_child("PSCD");
//给节点增加属性，并赋值
root.append_attribute("name")="test";
//增加子节点
xml_node child_node=root.append_child("child");
//**增加给节点赋值text**
xml_node childText_node=root.append_child("textNode");
childText.append_child(pugi::node_pcdata).set_value("text");
//保存文件，这里特别要说明的一个问题就是参数的设置：pugi::format_no_escapes。设置成这样才能正确输出特殊符号"<,&"等
doc.save_file("outFile.xml","\t",pugi::format_no_escapes,pugi::encoding_utf8);
```
