---
layout:     post
title:      Slow HTTP DOS
date:       2020-05-08
author:     lceCr4m
header-img: img/http.png
catalog: true
tags:
    - HTTP
    - Slow HTTP DOS
---

## 0x00 Slow HTTP DOS介绍
Slow HTTP DOS(Slow HTTP Denial of Service Attack)，译为缓慢的HTTP拒绝服务。  

Slow HTTP DOS是一个应用层拒绝服务攻击，主要针对协议为HTTP，攻击的成本很低，并且能够消耗服务器端资源，占用客户端连接数，导致正常用户无法连接服务器。 

Slow HTTP DOS不像DDOS那么直接通过大量请求搞崩服务器，它折磨服务器，让服务器感觉各种不自在，然后慢慢死去。

## 0x01 Slow HTTP DOS原理  
大概原理是让服务器等待，当服务器在保持连接等待时，自然就消耗了资源，达到攻击目的。  
HTTP协议的报文都是一行一行的，比如：  
```
GET / HTTP/1.1\r\n
Host : payloads.online\r\n
Connection: keep-alive\r\n
Keep-Alive: 900\r\n
Content-Length: 100000000\r\n
Content_Type: application/x-www-form-urlencoded\r\n
Accept: *.*\r\n
\r\n
```
其中的\r\n代表一行报文的结束也被称为空行（CRLF），\r\n\r\n代表整个报文的结束。  

由于http协议是无状态协议，如果为非KeepAlive模式时，每个请求/应答客户和服务器都要新建一个连接，完成之后立即断开连接；当使用KeepAlive模式时，避免了建立或者重新建立连接。  

如果我们构造报文，使得报文不以\r\n\r\n结尾（意味着报文未结束），每隔10秒发一行报文，那么上述报文需要80秒，服务器需要一直等待客户端报文的结束，然后才能解析这个报文，服务器资源就被占用了。  

如果我们使用更多的程序发送这样的报文，那么服务器端会给客户端留出更多的资源来处理、等待这迟迟不传完的报文。假设服务器端的客户端最大连接数是100个，我们使用测试程序先连接上100次服务器端，并且报文中启用Keep-Alive，那么其他正常用户101等就无法正常访问网站了。

## 0x02 利用slowhttptest实施Slow HTTP DOS  
使用slowhttptest进行测试：(https://github.com/shekyan/slowhttptest)



## 0x03修复方案  

## 参考




