---
title: websocket
date: 2020-07-09 15:41:02
tags: ["websocket"]
categories: ["websocket"]
---

# Websocket 是什么？

WebSocket协议是html5的一种通信协议，该协议兼容我们常用的浏览器

WebSocket 是一种在单个 TCP 连接上进行全双工通信的协议。
WebSocket 通信协议 诞生于 2011 年，WebSocket API 也被 W3C 定为标准。
Websocket 允许服务器向客户端推送数据，并且只需要完成一次握手，就能持续的进行双向数据传输
 WS协议和WSS协议两个均是WebSocket协议的SCHEM，两者一个是非安全的，一个是安全的。也是统一的资源标志符。

WSS表示在TLS之上的WebSocket。WS一般默认是80端口，而WSS默认是443端口，大多数网站用的就是80和433端口

websocket分为握手和数据传输阶段，即进行了HTTP握手 + 双工的TCP连接。

#### 1.客户端和服务器如何完成握手

Websocket 通过 HTTP/1.1 协议的 101 状态码进行握手。
为了创建 Websocket 连接，需要通过浏览器发出请求，之后服务器进行回应，这个过程通常称为“握手”（handshaking）。

![image-20200821160238248](C:\Users\ymshan\AppData\Roaming\Typora\typora-user-images\image-20200821160238248.png)



#### 2. API

##### 1. 构造函数

WebSocket 对象，用于创建和管理Websocket 连接

WebSocket(url [, protocols])   返回一个WebSocket 对象

##### 2. WenSocket.readyState 

实例对象的当前状态

| 常量                 | value | 意义                   |
| -------------------- | ----- | ---------------------- |
| WebSocket.CONNECTING | 0     | 正在连接               |
| WebSocket.OPEN       | 1     | 连接成功               |
| WebSocket.CLOSING    | 2     | 正在关闭               |
| WebSocket.CLOSED     | 3     | 已经关闭 \|\| 连接失败 |

##### 3. 属性

| 属性名     | 意义                     |      |
| ---------- | ------------------------ | ---- |
| url        | 绝对路径                 |      |
| readyState | 链接状态                 |      |
| onopen     | 连接成功的回调           |      |
| onmessage  | 从服务器接受到信息的回调 |      |
| onerror    | 连接失败的回调           |      |
| onclose    | 关闭连接的回调           |      |
|            |                          |      |



##### 4. 方法

| 方法名                                       | 意义         | 备注 |
| -------------------------------------------- | ------------ | ---- |
| WebSocket.close( [ code  [ ,   reason ]  ] ) | 关闭当前连接 |      |
| WebSocket.send( data )                       | 传输数据     |      |



##### 5. 事件

通过addEventListener 监听，也可以在对象对应属性设置监听

| 事件    | 意义                                       |      |
| ------- | ------------------------------------------ | ---- |
| close   | 连接被关闭时触发                           |      |
| error   | 连接因错误而关闭时触发，例如无法发送数据时 |      |
| message | 收到数据时触发                             |      |
| open    | 连接成功时触发                             |      |



### 应用场景

· 即时聊天通信

· 多玩家游戏

· 在线协同编辑/编辑

· 实时数据流的拉取与推送

· 体育/游戏实况

· 实时地图位置

 



参考：
[阮老师资料](http://www.ruanyifeng.com/blog/2017/05/websocket.html)