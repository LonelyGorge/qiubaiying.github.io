---
layout:     post
title:      X-Frame-Options头丢失--Clickhijacking
date:       2020-05-09
author:     lceCr4m
header-img: img/Clickhijacking.png
catalog: true
tags:
    - Clickhijacking
---
## 0x00 介绍
X-Frame-Options是HTTP响应头的一种，它可以指示浏览器是否应该加载一个iframe 中的页面。这种攻击利用了HTML中<iframe>标签的透明属性。
网站管理员可以通过设置X-Frame-Options阻止站点内的页面被其他攻击者构造的非法页面嵌入导致的点击劫持。  

当X-Frame-Options的HTTP响应头丢失的时候，攻击者可以伪造一个页面，该页面使用前端技术精心构造一些诱惑用户点击的按钮，
该元素下方就是一个iframe标签，当用户点击后上层的元素后，就相当于点击了iframe标签引入的网页页面，点击劫持的危害也是可大可小。
## 0x01 检测
使用CURL请求网站，查看响应头是否包含X-Frame-Options
```
curl -I http://<ip>
```
## 0x02 修复
配置WebServer，更改配置文件，添加自定义响应头  
使用 X-Frame-Options 有三个可选的值：  
```
DENY：浏览器拒绝当前页面加载任何Frame页面
SAMEORIGIN：frame页面的地址只能为同源域名下的页面
ALLOW-FROM：origin为允许frame加载的页面地址
```
## 0x03 参考
https://payloads.online/archivers/2018-04-16/3















