# 概述
客户端（client）通过发送请求获取服务器的资源（resource）
通信使用的是HTTP协议（HyperText Transfer Protocol，超文本传输协议）来完成从客户端到服务器端等一系列流程

# TCP/IP协议
TCP/IP协议为4层，从上往下分别为应用层、传输层、网络层和数据链路层

应用层：HTTP协议、DNS服务

传输层：TCP和UDP协议

网络层：IP

链路层：网络连接硬件部分

# IP、TCP和DNS概念
IP（Internet Protocol）协议：将数据包传送给对方

TCP协议：提供可靠的字节流服务，为保证数据的准确性采用三次握手策略

DNS（Domain Name System）服务：提供域名到IP地址之间的解析服务

# URI和URL
URL（Uniform Resource Locator）：统一资源定位符，表示资源的地址（互联网上所处的位置）

URI（Uniform Resource Identifier）：统一资源标识符，用字符串表示某一互联网资源

URL是URI的子集

# http协议
请求行
请求头
空行
请求体

# 返回结果的HTTP状态码
几个常用的状态码：

200 OK

表示从客户端发送来的请求在服务端被正常处理

204 No Content

表示服务器接受的请求已被成功处理，但是在返回的响应报文中不含实体的主体部分

206 Partial Content

表示客户端进行了范围请求，而服务器成功执行了这部分的GET请求

301 Moved Permanently

表示永久重定向，即请求的资源被分配给新的URI，以后应该使用现在的URI访问资源

302 Found

表示临时性重定向，即请求的资源被分配给新的URI，希望用户（本次）能使用新的URI访问

303 See Other

与302类似，但明确规定使用GET方法定向获取请求资源

304 Not Modified

表示客户端发送附带条件的请求时，服务端允许请求访问资源，但请求未满足条件

307 Temporary Redirect

表示临时重定向，与302相同，但307会遵循浏览器标准，不会将POST变成GET

400 Bad Request

表示请求报文中存在语法错误，需修改请求内容后再次发送请求。另外，浏览器会像200一样对待该状态码

401 Unauthorized

表示发送的请求需要有通过HTTP认证的认证信息，若之前已进行过1次请求，则表示用户认证失败

403 Forbidden

表示对请求资源的访问被服务器拒绝

404 Not Found

表示无法在服务器上找到请求的资源

500 Internal Server Error

表示服务器端在执行请求时发生错误

503 Service Unavailable

表示服务器暂时处于超负载或正在停机维护，现在无法处理请求

# 常见请求头
User-Agent

书写方式：User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.93 Safari/537.36
意思：用来标识客户端（通常是浏览器）的用户代理字符串，即客户端的身份标识。服务器可以根据用户代理来适配响应内容。
Host

书写方式：Host: www.example.com
意思：指定请求的目标服务器的主机名和端口号。
Content-Type

书写方式：Content-Type: application/json
意思：指定请求体中所发送的实体的媒体类型。
常见取值：application/json（JSON数据）、application/x-www-form-urlencoded（表单数据）、multipart/form-data（上传文件）
Accept

书写方式：Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,/;q=0.8
意思：指定客户端可接受的响应内容类型。服务器可以根据该头部信息选择性地返回对应类型的响应。
Authorization

书写方式：Authorization: Bearer token_value
意思：用于在请求中发送身份验证凭据，例如令牌（Token）或用户名和密码。常用于API的身份验证。
Cookie

书写方式：Cookie: key1=value1; key2=value2
意思：向服务器传递之前设置的Cookie值，用于在客户端和服务器之间跟踪会话状态。
Referer

书写方式：Referer: https://www.example.com/
意思：指定当前请求的来源页面的URL。服务器可以根据Referer头部来进行一些业务逻辑处理。

# 响应头
Content-Type

书写方式：Content-Type: text/html; charset=UTF-8
意思：指定响应实体的媒体类型和字符集。
Content-Length

书写方式：Content-Length: 1234
意思：指定响应实体的长度，单位为字节。
Date

书写方式：Date: Sun, 02 Jan 2022 23:23:27 GMT
意思：指定响应发送的日期和时间。
Server

书写方式：Server: Apache/2.4.48 (Unix) OpenSSL/1.1.1k PHP/7.4.25
意思：服务器软件的名称和版本号。
Set-Cookie

书写方式：Set-Cookie: name=value; expires=Sat, 02-Jan-2022 23:23:27 GMT; path=/
意思：设置一个新的Cookie值，用于在客户端和服务器之间跟踪会话状态。
Location

书写方式：Location: https://www.example.com/newpage.html
意思：指定重定向的URL。
Cache-Control

书写方式：Cache-Control: no-cache
意思：指示客户端缓存如何管理响应数据。
