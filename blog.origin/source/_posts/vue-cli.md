---
title: vue/cli
date: 2020-04-12 14:15:05
tags: [vue, cli]
categories: [vue]
---

快速搭建项目
1. 安装 cli2.0

前提： node  npm 

npm install webpack -g   //全局安装webpack

npm install vue-cli -g

淘宝镜像：

npm install -g cnpm --registry=https://registry.npm.taobao.org

使用：

建立项目之后 vue init webpack 项目名

进入项目目录（ 有 mode_modules）

cnpm install  > npm run dev

 项目结构：
 ```
 |-- build                            // 项目构建(webpack)相关代码
|   |-- build.js                     // 生产环境构建代码
|   |-- check-version.js             // 检查node、npm等版本
|   |-- utils.js                     // 构建工具相关
|   |-- vue-loader.conf.js           // webpack loader配置
|   |-- webpack.base.conf.js         // webpack基础配置
|   |-- webpack.dev.conf.js          // webpack开发环境配置,构建开发本地服务器
|   |-- webpack.prod.conf.js         // webpack生产环境配置
|-- config                           // 项目开发环境配置
|   |-- dev.env.js                   // 开发环境变量
|   |-- index.js                     // 项目一些配置变量
|   |-- prod.env.js                  // 生产环境变量
|   |-- test.env.js                  // 测试脚本的配置
|-- src                              // 源码目录
|   |-- components                   // vue所有组件
|   |-- router                       // vue的路由管理
|   |-- App.vue                      // 页面入口文件 （页面级Vue组件）
|   |-- main.js                      // 程序入口文件，加载各种公共组件
|-- static                           // 静态文件，比如一些图片，json数据等
|-- test                             // 测试文件
|   |-- e2e                          // e2e 测试
|   |-- unit                         // 单元测试
|-- .babelrc                         // ES6语法编译配置【转换成ES5】
|-- .editorconfig                    // 定义代码格式【编码、缩进】
|-- .eslintignore                    // eslint检测代码忽略的文件（夹）
|-- .eslintrc.js                     // 定义eslint的plugins,extends,rules
|-- .gitignore                       // git上传需要忽略的文件格式
|-- .postcsssrc                      // postcss配置文件
|-- README.md                        // 项目说明，markdown文档
|-- index.html                       // 访问的页面
|-- package.json                     // 项目基本信息,项目开发包依赖信息等，scripts【npm命令配置】-dependencies【 项目运行时所依赖的模块】-  devDependencies- 【项目开发所依赖的模块】
|-- node_modules                     //依赖的node工具包目录【安装依赖后出现】
 ```


2. 升级到3.0版本
卸载 2.0 npm uninstall vue-cli -g

安装 npm install -g @vue/cli

 .vue.config.js 配置 
 ```
 const webpack = require("webpack");

module.exports = {
  publicPath: process.env.NODE_ENV === "production" ? "" : "./", //部署路径(相对路径), 非默认
  // outputDir: "dist", // 构建文件目录
  assetsDir: "public", // 构建后静态资源目录(js、css、img、fonts),非默认
  indexPath: "index.html",
  pages: {
    index: {
      entry: "src/main.js", // page入口文件
      template: "public/index.html", // 模板来源
      filename: "index.html", // 在 dist/index.html 的输出
      title: "XXX系统", // 当使用 title 选项时，,template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>
      chunks: ["chunk-vendors", "chunk-common", "index"] // 在这个页面中包含的块，默认情况下会包含,提取出来的通用 chunk 和 vendor chunk
    }
  },
  lintOnSave: process.env.NODE_ENV !== "production", // 开发环境下,每次保存lint代码
  productionSourceMap: true, // 生产环境是否生成 sourceMap 文件
  devServer: {
    host: "0.0.0.0",
    port: "8080",
    https: false,
    hotOnly: false,
    open: true, //自动启动浏览器
    proxy: {
      // "": {
      //   target: "",
      //   ws: true,
      //   changeOrigin: true
      // }
      "/index.php": {
        // target: 'http://wholesale.linli580.com.cn',
        // target: 'http://wholesale.linli580.com.cn',
        // target: 'http://wholesale.linli580.com.cn',
        target: "http://apiv2.linli580.com.cn/wholesale",
        // target: 'http://mall.linli580.com.cn',
        changeOrigin: true,
        secure: false
        // pathRewrite: {
        //   '^/index.php': '' //需要rewrite重写
        // }
      },
      "/api": {
        // target: 'http://wholesale.linli580.com.cn',
        // target: 'http://wholesale.linli580.cn',
        target: "http://wholesale.linli580.com.cn",
        changeOrigin: true,
        secure: false
        // pathRewrite: {
        //   '^/index.php': '' //需要rewrite重写
        // }
      },
      "/admin": {
        // target: 'http://wholesale.linli580.com.cn',
        target: "http://wholesale.linli580.com.cn",
        // target: 'http://mall.linli580.com.cn',
        changeOrigin: true,
        secure: false
        // pathRewrite: {
        //   '^/index.php': '' //需要rewrite重写
        // }
      }
    }
  },
  configureWebpack: {
    plugins: [
      new webpack.ProvidePlugin({
        "window.Quill": "quill/dist/quill.js",
        Quill: "quill/dist/quill.js"
      })
    ]
  },
  pluginOptions: {
    //不进行任何 schema 验证的对象，因此它可以用来传递任何第三方插件选项
  }
  // configureWebpack: config => {
  //   if (process.env.NODE_ENV === "production") {
  //     // 生产环境配置
  //     console.log(config);
  //   } else {
  //     // 开发环境配置
  //     console.log(config);
  //   }
  // }
};
        

 ```

## 实现原理
1.commander.js  node.js命令行界面的完整解决方案

未完待续......

