title: python-http包
date: 2014-11-18 21:26:07
categories: python
tags:
---
httplib:(renamed to http.client in Python3)
	实现了http和https协议
	一般不被直接使用，urllib建立在其之上
	https是否能被支持仅当socket能否支持https

urllib(在python3后被细化到urllib.request,urllib.parse和urllib.error):
	通过url打开资源
	编码解码

urllib2:(在python3后被细化到urllib.request和urllib.error)
	urllib的扩展库
	增加认证、重定向、响应时间和cookie功能等
	可以接受一个Request类的实例来设置URL请求的header

urllib3:(not offical)
	urllib2的扩展库
	提供连接池和文件post功能

requests:(not offical)
	based on urllib3
	build for human beings

httplib2:(not offical, by google)
	实现了httplib没有实现的一些功能
	目的是代替httplib,to become a worthy addition to the standard Python library.

1.http/https get 无参数
2.http/https get 带参数
3.http/https post 无数据
4.http/https post 带数据
