---
title: pyspider爬虫的编码
categories: others
tags:
  - pyspider
date: 2017-02-08 12:18:23
---

最近用pyspider抓取网页，pysider内置html解析库是pyquery。  
在读取百度首页标题的时候报错。很明显`u'\xe7\x99\xbe\`并不是   
unicode，可能是pyquery转换过程中出了错误，所以打印也会报错。 
```python
>>> #coding:utf-8
>>> from pyquery import PyQuery
>>> pq=PyQuery(url="https://www.baidu.com/")
>>> pq("title).text()
u'\xe7\x99\xbe\xe5\xba\xa6\xe4\xb8\x80\xe4\xb8\x8b\xef\xbc\x8c\xe4\xbd\xa0\xe5\xb0\xb1\xe7\x9f\xa5\xe9\x81\x93'
>>> print pq("title).text()
UnicodeEncodeError: 'gbk' codec can't encode character u'\xe7' in position 0: illegal multibyte sequence
```
<!--more-->
此时用`encode('utf-8').decode('gbk)`也无济于事。还好python  
提供了一个特殊的编码`raw_unicode_escape`用来处理这种情况：

```python
>>> title=pq("title).text().encode('raw_unicode_escape').decode('utf8')
>>> title
u'\u767e\u5ea6\u4e00\u4e0b\uff0c\u4f60\u5c31\u77e5\u9053'
>>> print title
百度一下，你就知道
```
其实去掉前缀u就能正确识别:
```python
>>> title='\xe7\x99\xbe\xe5\xba\xa6\xe4\xb8\x80\xe4\xb8\x8b\xef\xbc\x8c\xe4\xbd\xa0\xe5\xb0\xb1\xe7\x9f\xa5\xe9\x81\x93'
>>> print title.decode('utf-8)
百度一下，你就知道
```