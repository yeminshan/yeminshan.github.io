---
title: http
date: 2020-03-19 22:10:58
tags: [http]
categories: [http]
---

### 1.一次完整的HTTP事务流程
（1）域名解析【网址到IP地址的转换】【DNS解析浏览器缓存-系统缓存【host文件】- 路由器缓存-ISP（服务提供商）DNS缓存本地域名服务器-根域名服务器-顶级域名服务器-主域名服务器】

（2）发起TCP的三次握手

（3）建立TCP连接后发起http请求

（4）服务器响应http请求，浏览器得到HTML代码

（5）浏览器解析HTML代码，并请求HTML代码中的资源

（6）浏览器对页面进行渲染呈现给用户

（7）连接结束

### 2.HTTP状态码
#### 分类

（1）1**：信息，服务器收到请求，需要请求者继续执行操作

（2）2**：成功，操作被成功接收并处理

（3）3**：重定向，需要进一步的操作以完成请求

（4）4**：客户端错误，请求包含语法错误或者无法完成请求

（5）5**：服务器错误，服务器在处理请求的过程中发生了错误

#### 常见的HTTP状态码：

200——OK，请求成功
301——Moved Permanently，资源（网页等）被永久转移到其他URL
302——Found，307——Temporary Redirect，临时重定向，请求的文档被临时移动到别处
304——Not Modified，未修改，表示客户端缓存的版本是最近的
401——Unauthorized，请求要求用户的身份认证
403——Forbidden，禁止，服务器理解客户端请求，但是拒绝处理此请求，通常是权限设置所致
404——Not Found，请求的资源（网页等）不存在
500——Internal Server Error——内部服务器错误
502——Bad Gateway，充当网关或代理的服务器从远端服务器接收到了一个无效的请求
504——Gateway Time-out，充当网关或代理的服务器，未及时从远端服务器获取请求

 ### 3.浏览器同源策略

（1）“同源策略”三个相同”：

协议相同
域名相同
端口相同
（2）同源策略的目的是为了保证用户信息的安全，防止恶意的网站窃取数据。不同源的客户端脚本在没有明确授权的情况下，不能读写对方的资源。

（3）如果非同源，共有三种行为受到限制：

Cookie、LocalStorage和IndexDB无法读取
DOM无法获得
AJAX请求不能发送

（4）不受同源策略限制的：

页面中的链接，重定向以及表单提交
跨域资源的引入是可以的，但是js不能读写加载的内容。如:
```
<script src=.../></script>，<img>，<link>，<iframe>
```

 ### 4.跨域

（1）受同源策略的限制，不是同源的脚本不能操作其他源下面的对象。想要操作另一个源下的对象，就需要跨域。

（2）跨域的实现方式

降域 document.domain
设置窗口片段标识符，监听读取location.hash
window.name
HTML5的postMessage方法
LocalStorage
JSONP
WebSocket
CORS

### 5.HTTP& HTPPS

HTTPS在HTTP的基础上加入了SSL/TLS，依靠证书来验证服务器的身份，并为浏览器和服务器之间的通信加密。SSL（Secure Socket Layer，安全套接层），TLS（Transport Layer Securit，传输层安全协议）

（1）HTTPS协议需要CA申请证书，一般免费证书比较少，所以需要一定费用

（2）HTTP是超文本传输协议，信息是明文传输，HTTPS则是具有安全性的SSL加密传输协议

（3）HTTP和HTTPS使用的是完全不同的连接方式，使用的端口号也不一样，前者是80，后者是443

（4）HTTP连接很简单无连接，是无状态的；HTTPS协议是由HTTP+SSL协议构建的可进行加密传输、身份认证的网络协议，比较安全。

（5）谷歌搜索引擎算法中，比起同等HTTP网站，采用HTTPS加密的网站在搜索结果中排名会更高


### 6.[图解http](https://www.processon.com/view/link/58025201e4b0d6b27dd4c8af#map)

### 7.GET 与 POST 对比
http 响应报文
三部分组成： 状态行、响应头(Response Header)、响应正文


1.http URL：
GET http URL 的长度有限制（3k）;
POST http URL 的长度没有限制，需要制定传输类型，请求头多了content-Type 和 Content-Length

2.请求信息：
GET 请求信息在 URL 里
POST 请求信息在 请求body 里

3.post  请求数据格式

content-type:application/x-www-form-urlencoded 【form表单默认的数据格式】 按key1=val1&key2=val2URL 转码    Query String Parameters 

content-type: application/json     请求数据格式必须是json（用JSON.stringfy处理过的   Request Payload  （会有{}）

content-type: multipart/form-data 用于表单上传，多用于上传文件  Form Data