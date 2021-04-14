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

  **优势：**

处理大流量数据
特别适合实时应用
完美支持对象型数据库
异步处理大量并发连接

**Javascript与Nodejs的异同:**

**javaScript:**

ECMAScript:语言基础，如：语法、数据类型结构以及一些内置对象

DOM:操作页面元素方法

BOM:操作浏览器方法

**Node.jsS**

ECMAScript:语言基础，如：语法、数据类型结构以及一些内置对象

os:操作系统

file:文件系统

net:网络系统

database:数据库

## 2.Node.js 应用是由哪几部分组成的：

**引入 required 模块**：我们可以使用 require 指令来载入 Node.js 模块。(自带http模块)

**创建服务器**：服务器可以监听客户端的请求，类似于 Apache 、Nginx 等 HTTP 服务器。

**接收请求与响应请求**： 服务器很容易创建，客户端可以使用浏览器或终端发送 HTTP 请求，服务器接收请求后返回响应数据。

# 3.Nodejs应用的主文件index.js的组成部分

1. 引入依赖模块

2. 设置相关配置

3. **连接数据库（可选）**

4. 定义中间件

5. 定义路由

6. 开启服务

7. **在多核系统上启动cluster多核处理模块（可选）**

   ```node.js
   //引用依赖模块
   var express = require('express');
   var path = require('path');
   var favicon = require('serve-favicon');
   var logger = require('morgan');
   var cookieParser = require('cookie-parser');
   var bodyParser = require('body-parser');
   
   var routes = require('./routes/index');
   var users = require('./routes/users');
   
   var app = express();
   
   // 视图引擎设置
   app.set('views', path.join(__dirname, 'views'));
   app.set('view engine', 'jade');
   
   //中间件
   app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger('dev'));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, 'public')));
   
   //路由
   app.use('/', routes);
   app.use('/users', users);
   
   //遇到错误时的中间件
   app.use(function(req, res, next) {
     var err = new Error('Not Found');
     err.status = 404;
     next(err);
   });
   
   if (app.get('env') === 'development') {
     app.use(function(err, req, res, next) {
       res.status(err.status || 500);
       res.render('error', {
         message: err.message,
         error: err
       });
     });
   }
   
   app.use(function(err, req, res, next) {
     res.status(err.status || 500);
     res.render('error', {
       message: err.message,
       error: {}
     });
   });
   
   //抛出 提供给www.js 并通过www.js来开启服务
   module.exports = app;
   ```

   

### 3.npm install 安装包
npm install XX ，并且还会生成package.json，并将安装包信息记录在devDependencies或dependencies

### 4.npm init 生成package.json,用来记录这个项目的详细信息的；创建npm 项目

### 5.node事件循环


## node 版本管理工具 nvm
1. 安装(mac: )
```
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

```


2. 安装淘宝镜像
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
mac安装不成功时：
```
npm install -g cnpm --registry=https://registry.npm.taobao.org --verbose  
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org --verbose

```

> .bash_profile问题
>
> 方法：1
>
> ```
> touch ~/.bash_profile
> curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
> ```
>
> open ~/.bash_profile *打开.bash_profile文件，把里面的内容copy到其它地方备份，然后关闭这个文件。*
>
> rm ~/.bash_profile*二 删除.bash_profile文件*
>
> *执行这行命令后会有提示 【override rw-r--r--  root/staff for /Users/hyu/.bash_profile?】* *# 输入【y】即可。*
>
> touch ~/.bash_profile  *三 新建.bash_profile文件*
>
> open ~/.bash_profile *四 打开恢复刚刚备份的内容*
>
> source ~/.bash_profile *刷新配置文件（其实应该不刷新也可以，因为一会安装nvm会自动刷新）*

> window： 安装地址：https://github.com/coreybutler/nvm-windows/releases 推荐nvm-setup.zip
>
> 输入nvm 验证，若不成功：
>
> 1.重启电脑。
>
> 2.手动添加环境变量
>
> 安装软件根目录找到settings.txt ，新建系统变量  NVM_HOM：settings.txt root的值；NVM_SYMLINK：settings.txt path的值。在path 追加
>
> ```
> ;%NVM_HOME%;%NVM_SYMLINK%;
> ```
>
> 你安装不成功，安装软件根目录找到settings.txt 追加
>
> ```ruby
> root: XXX // 原有的
> path: XXX // 原有的
> node_mirror: https://npm.taobao.org/mirrors/node/
> npm_mirror: https://npm.taobao.org/mirrors/npm/
> ```


node 版本管理工具 gnvm

1.将 gnvm.exe 复制到 node 安装目录下 （先安装 node 的情况，未安装时，直接新建一个文件夹，将 gnvm.exe 存放进去。添加该文件夹环境变量）

2.设置淘宝源 ：

```
gnvm config registry TAOBAO
```

3.常用命令

```
gnvm ls                         # 查看node 版本
gnvm search *.*.*               # 查询node版本
gnvm use XXXX                   # 切换node版本
gnvm install X.X.X              # 安装某版本
gnvm uninstall X.X.X            # 卸载
```


