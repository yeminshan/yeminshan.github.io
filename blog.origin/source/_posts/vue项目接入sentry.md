---
title: vue项目接入sentry
date: 2020-03-19 22:53:52
tags:
categories: [vue, sentry]
---
# 前言
  为了自主监听前端项目在测试和运行中出现的错误，达到高校定位问题所在，完善和修复项目开发中没有周全考虑的问题等产生的bug。
  Points：主动 纠错 定位 修复

  原理：
  跟踪运行时错误，如果在组件渲染时出现运行错误，错误将会被传递至全局 Vue.config.errorHandler 配置函数 (如果已设置)。利用这个钩子函数来配合错误跟踪服务是个不错的主意。Sentry，为Vue提供官方集成。

# 1.sentry 注册 & 添加sentry项目
  注册
  添加sentry项目
  登陆sentry - create projiect - browsers - vue.js
# 2.vue项目安装插件
    npm install @sentry/browser
    npm install @sentry/integrations
# 3.vue 项目引入
    import * as Sentry from '@sentry/browser';
    import * as Integrations from '@sentry/integrations';
    Sentry.init({
    dsn: 'https://9257bb5c60814f518e012fb3d0e9dda6@sentry.io/4320951', // 不同的项目dsn不同
    integrations: [new Integrations.Vue({Vue, attachProps: true // 是否上报组件的 props})],
    });    
# 4.其他设置
## 仅仅设置生产环境才上传错误
使用 process.env.NODE_ENV === 'product' 判断
## 接入后浏览器控制器没有任何报错
设置 logErrors: true，本地控制台会像正常报错，否则上传错误而不报错。
## vue项目上传的错误信息是压缩后的
[sentry官网](https://docs.sentry.io/platforms/javascript/sourcemaps/) 这里有说解决指引,建议来这里看看啦

    If you are uploading source map artifacts yourself, you must specify the release in your SDK. Sentry will use the release name to associate digested event data with the files you’ve uploaded via the releases API, sentry-cli or sentry-webpack-plugin. This step is optional if you are hosting source maps on the remote server.


上传source-map 解决思路:通过release name 和 sentry-cli or sentry-webpack-plugin 的上传进行关联


 sentry-webpack-plugin

### 1.在process定义一个release标识
例如：process.env.RELEASE_VERSION = v1.0.0。在vue-cli2.0版本里面，npm run build，执行 node build.js , 在这里添加这样的标识是不错的，当然啦很多人说webpack.prod.conf.js里面添加也是可以的。
然而但是，我在项目中接入的时候，发现输出整个process.env发现只有{NODE_ENV:'product' },惊不惊喜意不意外，那就找到plugins：[new webpack.DefinePlugin({ 'process.env': env (这是个引用值)})]，修改对应的修改吧。
### 2.引入插件
    npm install --save-dev @sentry/webpack-plugin
    npm install @sentry/webpack-plugin --only=dev

```js
import SentryCliPlugin from '@Sentry/webpack-plugin'
plugins:[
  new SentryCliPlugin({
      include: "./dist", // 需要上传到sentry服务器的资源目录,会自动匹配js 以及map文件
      release: process.env.RELEASE_VERSION, // 版本号
      configFile: "sentry.properties", // 不用改
      ignore: ['node_modules', 'webpack.config.js'],
      urlPrefix: "~/static/"  // 线上对应的url资源的相对路径，打包后默认在dist，并且里面包含 index.html 和 static 文件夹放置静态资源
    })
]
```

### 3.sentry init 添加版本
```js
Sentry.init({
  release: processenv.RELEASE_VERSION
})
```

### 4.添加 .sentryclirc 文件
    [auth]
    token=db1bf055c63640408762... // settings-- API keys -- Auth tockens-- create new token
 
    [defaults]
    url = http://log.XX.cn // 服务器地址
    org = sentry // 组织名称，settings-- Gerneral settings -- settings
    project = llb // 项目名称
