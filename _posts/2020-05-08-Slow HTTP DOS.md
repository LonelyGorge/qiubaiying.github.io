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
使用slowhttptest进行测试：https://github.com/shekyan/slowhttptest  

slowhttptest是一个可配置的应用层拒绝服务攻击测试攻击，它可以工作在Linux，OSX和Cygwin环境以及Windows命令行接口，可以帮助安全测试人员检验服务器对慢速攻击的处理能力。  

这个工具可以模拟低带宽耗费下的DoS攻击，比如慢速攻击，慢速HTTP POST，通过并发连接池进行的慢速读攻击（基于TCP持久时间）等。慢速攻击基于HTTP协议，通过精心的设计和构造，这种特殊的请求包会造成服务器延时，而当服务器负载能力消耗过大即会导致拒绝服务。  

常用：SlowLoris模式，Slow POST模式，Range Header模式，Slow Read模式  

参数：  
```
 -g      在测试完成后，以时间戳为名生成一个CVS和HTML文件的统计数据
 -H      SlowLoris模式
 -B      Slow POST模式
 -R      Range Header模式
 -X      Slow Read模式
 -c      number of connections 测试时建立的连接数
 -d      HTTP proxy host:port  为所有连接指定代理
 -e      HTTP proxy host:port  为探测连接指定代理
 -i      seconds 在slowrois和Slow POST模式中，指定发送数据间的间隔。
 -l      seconds 测试维持时间
 -n      seconds 在Slow Read模式下，指定每次操作的时间间隔。
 -o      file name 使用-g参数时，可以使用此参数指定输出文件名
 -p      seconds 指定等待时间来确认DoS攻击已经成功
 -r      connections per second 每秒连接个数
 -s      bytes 声明Content-Length header的值
 -t      HTTP verb 在请求时使用什么操作，默认GET
 -u      URL  指定目标url
 -v      level 日志等级（详细度）
 -w      bytes slow read模式中指定tcp窗口范围下限
 -x      bytes 在slowloris and Slow POST tests模式中，指定发送的最大数据长度
 -y      bytes slow read模式中指定tcp窗口范围上限
 -z      bytes 在每次的read()中，从buffer中读取数据量
```

## 0x03修复方案  
1.设置合适的 timeout 时间（Apache 已默认启用了 reqtimeout 模块），规定了 Header 发送的时间以及频率和 Body 发送的时间以及频率。  

2.增大 MaxClients(MaxRequestWorkers)：增加最大的连接数。根据官方文档，两个参数是一回事，版本不同，MaxRequestWorkers was called MaxClients before version 2.3.13. The old name is still supported。  

3.默认安装的 Apache 存在 Slow Attack 的威胁，原因就是虽然设置的 timeoute，但是最大连接数不够，如果攻击的请求频率足够大，仍然会占满 Apache 的所有连接。  

### Apache
1.mod_reqtimeout模块，默认启动。设置 header 和 body 的发送时间及频率。  
在 httpd.conf 里面，添加上如下： 
```
mod_reqtimeout.so
<IfModule reqtimeout_module>
    RequestReadTimeout header=20-40,minrate=500RequestReadTimeout body=10,minrate=500
</IfModule>
```
2.mod_qos：开源模块，需额外安装。  

3.mod_security：开源模块，需额外安装。

### Tomcat
Tomcat 在 server.xml 中修改超时时间即可  
![tomcat](https://github.com/lceCre4m/lceCre4m.github.io/blob/master/img/tomcat.jpg?raw=true)

### Weblogic
1.在配置管理界面中的协议->一般信息下设置 完成消息超时时间小于400  
![weblogic1](https://github.com/lceCre4m/lceCre4m.github.io/blob/master/img/weblogic1.jpg?raw=true)

2.在配置管理界面中的协议->HTTP下设置 POST 超时、持续时间、最大 POST 大小为安全值范围。  
![weblogic2](https://github.com/lceCre4m/lceCre4m.github.io/blob/master/img/weblogic2.jpg?raw=true)

## 0x04参考
https://payloads.online/archivers/2018-04-16/2  
https://www.cnblogs.com/v1vvwv/p/slowHTTPtest-attack-and-defense.html  





