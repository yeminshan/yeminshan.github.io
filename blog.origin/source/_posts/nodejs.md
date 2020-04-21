---
title: nodejs
date: 2020-03-19 22:42:24
tags: [node]
categories: [nodejs]
---
## 1.简介认识

    nodejs就是这个解释器（他不是一个语言）

    让javascript运行在服务端的开发平台（js运行环境）【Node.js给了javascript在服务器端运行环境，能在服务器运行】

    是一个基于 Chrome V8 引擎的 JavaScript 运行环境。内核是用了chrome的V8引擎。

    Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型。

    是一个服务器程序。

  优势：

处理大流量数据
特别适合实时应用
完美支持对象型数据库
异步处理大量并发连接
## 2.Node.js 应用是由哪几部分组成的：

引入 required 模块：我们可以使用 require 指令来载入 Node.js 模块。(自带http模块)

创建服务器：服务器可以监听客户端的请求，类似于 Apache 、Nginx 等 HTTP 服务器。

接收请求与响应请求 服务器很容易创建，客户端可以使用浏览器或终端发送 HTTP 请求，服务器接收请求后返回响应数据。

### 3.npm install 安装包
npm install XX ，并且还会生成package.json，并将安装包信息记录在devDependencies或dependencies

### 4.npm init 生成package.json,用来记录这个项目的详细信息的；创建npm 项目

### 5.node事件循环