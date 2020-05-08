---
layout:     post
title:      Slow HTTP DOS
date:       2020-05-08
author:     lceCr4m
header-img: img/helloworld.jpg
catalog: true
tags:
    - HTTP
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

## 0x02 利用slowhttptest实施Slow HTTP DOS  

## 0x03修复方案  

## 参考




