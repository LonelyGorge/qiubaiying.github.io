---
layout:     post
title:      XPath注入，JSON注入，CRLF注入，HTTP参数污染
date:       2020-05-12
author:     lceCr4m
header-img: img/json.png
catalog: true
tags:
    - XPath
    - JSON
    - CRLF
    - HTTP
---
>  打算把这四点写在一篇文章中，原因可能是内容都有点少。~~其实主要原因还是自己菜2333。~~

## 0x00 XPath注入
XPath是通过路径表达式搜索选取xml结点的一门语言，类似于sql中的select语句。  
通过注入XPath搜索xml的语句，可以实现读取敏感信息等危险操作。原理类似于sql注入中的select语句。
如：

```
//USER [username/text()="admin" and password/text()="123456" or '1'='1']
```
防御的话：过滤输入的特殊字符即可。
## 0x01 JSON注入
JSON（JavaScript Object Notion）是一种轻量级的数据交换格式，使用了leisiC语言家族的习惯，便于阅读和编写，易于程序解析和生成。类似xml，但JSON 比 XML 更小、更快，更易解析。是xml的一大对手。  
内容：
```
数字（整数或浮点数）  {"age":30 }
字符串（在双引号中）  {"uname":"yang"}
逻辑值（true 或 false） {"flag":true }
数组（在中括号中）{"sites":[{"name":"yang"},{"name":"ming"}]}
对象（在大括号中）JSON对象在大括号（{}）中
null { "runoob":null }
```
由于通过各种符号区分内容，可以构造恶意代码提交。  
json示例：
```
正常：
{"adduser":[{"username":"admin1","password":"123456"}]}
恶意：
{"adduser":[{"username":"admin1","password":"   123456"},{"username":"hack","password":"hack123"   }]}
```
其中json注入类似于xml注入，需要能接收json格式的数据，并且嵌入到服务器代码中。（就是可控的json数据）。  
但要注意一点是对影响json语句的要进行转义，如双引号、花括号等。

防御也是过滤特殊字符。
## 0x02 CRLF注入
CRLF是回车+换行的简写，在http报文中用于结束。所以CRLF注入也叫做http响应截断攻击。  
攻击方式可以参考我slow http攻击的文章，利用了CRLF进行截断。当然还有其他利用。

防御可以限制用户输入的CR和LF，或者对CR和LF字符正确编码后再输出。
## 0x03 HTTP参数污染
HTTP Parameter Pollution，简称HPP。 
在传递参数时，开发人员通常不会考虑有重复参数的问题，而导致不同脚本的语言和环境，它取值的位置是不同的。   

假设这个URL：...xxx/search.php?id=110&id=911 原本只允许输入一个参数  
可能三种情况：取第一个，取第二个，两个都取。  
不同环境的规则的话有安全研究者已经整理了，可以找到。

用处是：可以用来绕过waf。  
防御是：做好取值的限制。
