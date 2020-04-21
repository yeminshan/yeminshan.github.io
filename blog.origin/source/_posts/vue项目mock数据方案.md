---
title: vue项目mock数据方案
date: 2020-03-24 17:38:43
tags:
---


# Webpack mock数据
原理： 
webpack devserver中有一个设置可用来模拟接口返回数据：

    devServer.before
    function

    Provides the ability to execute custom middleware prior to all other middleware internally within the server. This could be used to define custom handlers, for example:

    before(app){
      app.get('/some/path', function(req, res) {
        res.json({ custom: 'response' });
      });
    }

实现：
```js
// 以下设置只针对开发环境，因此可在webpack.dev.config.js 设置(vuecli2.0+)
// 需要使用express，并设置
const express = require('express')
const app = express()
const apiRoutes = express.Router()

app.use('api', apiRoutes)

// 真正的好戏：
devServer.before(app) {
  // 单纯版
  // app.get('/api/seller', (req, res) => {
  //   res.json({
  //     errno: 0,
  //     data: [] // 数据
  //   })
  // }),
}

```
评价：这个比较麻烦，因为每一次添加mock规则都要重新跑一次webpack-dev-server


# mockjs
1. 安装mockjs

    npm install mockjs --save-dev

2.引入mock规则
```js
// 可在在main.js引入，在vue搭建的多页面项目中，千万要注意，入口文件可能不叫main.js，不要犯糊涂了！（在这里被坑了哭）
require('./mock.js')
```
3. mock.js
```js
// 引入mockjs
const Mock = require('mockjs');
const mockDate = {
        articles: 'XXXXX'
    }
// Mock.mock( url, methods [post | get] , 返回的数据)；
Mock.mock('/api', 'post', mockDate);
```

# yapi mock数据
yapi API 


# node实现一个简单的接口
这个方法还没有demo