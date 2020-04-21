---
title: webpack简单认识
date: 2020-03-21 15:34:51
tags: [webpack]
top: true

---


### 模块化
#### commonjsJs
广泛使用的javascript模块化规范，node.js采用这种方式得以流行。
缺点：无法直接运行在浏览器环境下

#### AMD
异步加载依赖模块，可直接在浏览器运行，但是javascript运行环境没有原生支持，需要倒入AMD库

#### ES6模块化
语言层面上实现模块化，浏览器和nodejs支持改规范

#### 样式文件的模块化如SCSS

### 新语言
#### ES6 js下一代标准
#### Typescript
js 超集，可被编译成ES5，ES6标准，支持ES6所有功能和静态类型检查。缺点：无法直接在浏览器和node环境运行

#### Flow
js超集，比ts灵活

### 构建
构建的主要工作：代码转换，文件优化，代码分割，模块合并，自动刷新，代码校验，自动发布。
#### 常用构建工具
##### Npm Script
任务执行者，是npm内置功能，通过package.json中scripts定义任务。底层通过调用shell运行脚本。无需安装其他依赖，缺点：太简单。

##### Grunt
进化版的npm script，集成度不高，无法开箱即用

##### Glup
基于流自动化构建工具，灵活好用，可单独构建，亦可搭配其他工具使用。缺点和Grunt一致。

##### Fis3
百度优秀国产构建工具，功能强大，集成各种web所需构建功能，但是官方不再维护且不支持最新版本nodejs

##### webpack
专注于构建模块化项目，缺点只能用于模块化项目。

##### Rollup
类似webpack，但不如webpack完善


# 安装
    npm i -D webpack@<version> 
    npm i --save-dev webpack@<version> 
    npm i -g webpack // 全局

  非全局安装成功后可以一下命令查看版本

    node_modules/webpack/bin/webpack.js -v
    node_modules/.bin/webpack -v // 安装后bin文件夹里会多了一个webpack可执行文件的软链

# 使用
webpack 执行构建，如果根目录存在webpack.config.js，默认会读取里面的配置。注意配置至少定义入口文件和输出文件。没有配置webpack.config.js文件也可执行。

    node_modules/webpack/bin/webpack.js <入口文件名> <输出文件名>
    node_modules/.bin/webpack <入口文件名> <输出文件名>
此时生成打包后文件会出现在根目录下。

# webpack 核心概念
webpack从entry递归解析出所有依赖的module，根据配置的loader进行转换，安装Entry进行分组，每个分组一个Chunk，最后将chunk转换成文件输出
1.  Entry
入口，webpack构建的第一步。

2. Modules
模块，webpack中一切皆可视为模块，webpack会从入口递归找出所有依赖模块。配置处理模块的规则，

3. Chunk
代码块，用于代码分割合并，一个chunk可以由多个模块生成。

4. Loader
转换器

5. Plugin
扩展插件

6. Output
输出结果

# 配置
配置webpack方式：
1. 通过js描述文件，如：webpack.config.js
2. webpack 可执行文件命令行传参 如：webpack--devtool source-map


## Entry 
配置模块入口，必填。
入口文件的路径可以是相对路径，webpack寻找相对路径文件以context为根目录，context默认值为执行启动webpack时所在的当前工作目录，且context必须是一个绝对路径的字符串。

修改context方式：
1. 配置文件设置

    module.export = {
      context: path.resolve(__dirname, 'app')
    }
2. 启动命令带参

    webpack --context XXX

类型 | 含义 | 示例
-|-|-
Sting | 单一入口，输出Chunk名称为main | entry：'index.js'
Array\<String> | 单一入口，仅会输出最后一个Chunk，名称为main | entry: ['index.js', 'home.js']
Object | 多入口,每个入口生成一个Chunk，Chunk名称为Object‘ s key name | entry: {<br> index: 'index.js',<br> home: 'home.js'<br> }

动态配置入口,函数动态返回入口配置
```js
entry：() => {
// 同步
  // return {
  //   XXX: 'XXXX',
  //   XXX: 'XXXX',
  // }
// 异步
  return new Promise ( (resolve)=> {
    resolve({
      XXX: 'XXXX',
      XXX: 'XXXX',
    });
  });
}
```

## Output 
内资变量表

变量名 | 含义
-|-
id | chunk唯一标识，从0开始
name | chunk名称
hash |  chunk唯一标识hash值,长度可指定，默认[hash:20]
chunkhash | chunk内容的hash,长度可指定，默认[hash:20]

```js
// Output 对象类型 
module.exports = {
  //...
  output: {
    // chunkFilename: 'XX', // 配置没入口的chunk输出文件名
    // filename: 'bundle.js', // <输出文件名> 配置单一入口时
    filename: '[name]_[chunkhash:8].js', //  <输出文件名> 配置多入口时
    path: __dirname + '/home/proj/cdn/assets/[hash]', // <输出文件时存放的本地目录> 必须时String类型的绝对路径
    // path: path.resolve(__dirname, 'dist_[hash]')可使用node 的path模块获取绝对路径 
    publicPath: 'http://cdn.example.com/assets/[hash]/', // <发布到线上异步资源URL前缀，相对路径，默认''> 谨慎使用很容易出错
    : '', 
  }
};
```

output其他常用属性 | 可选值 |  说明
-|-|-
crossOriginLoading |  anonymous,默认值。加载脚本不会带上用户的Cookies<br>use-credentials 会带上Cookies | jsonp原理引入script加载异步资源，用于设置异步引入标签的crossorigin值
library | 字符串 | 导出库的名称，常与libraryTarget一起使用
libraryTarget | 字符串枚举值，['var', 'commonjs', 'commonjs2', 'this', 'window', 'global']详情见[官网](https://webpack.docschina.org/configuration/output/#output-librarytarget) | 何种方式导出库
libraryExport | | 只有在libraryTarget设置为commonjs和commonjs2才有意义


## Module
Module属性 | 类型| 说明
-|-|-
noParse | RegExp,[RegExp],function | 设置让webpack忽略的无需模块化的文件，且被忽略文件不应该有模块化语句，如require，import，define 
rules | Array | 创建模块时，匹配请求的规则数组。这些规则能够修改模块的创建方式。这些规则能够对模块(module)应用 loader，或者修改解析器(parser)。<br>每个规则可以分为三部分 - 条件(condition)，结果(result)和嵌套规则(nested rule)<br>test、include、exclude选中应用loader的文件，指出字符串，正则，数组<br>use 设置应用loader数组，默认从右往左执行。enfore设置执行顺序的最前pre和最后post。

webpack内置了对js的解析功能，parser支持更细粒度地配置模块语法解析，其与noParse区别在于，前者精确到语法层面，后者只控制到文件。
```js
module: {
  rules: [{
    test: /\.js$/,
    use:['babel-loader'],
    parser:{
      amd: false, // 禁用AMD
      system: false, // 禁用system
      commonjs:false, // 禁用commonjs
      harmony: false，// 禁用ES6 import / export
      requireInclude: false, // 禁用require.include
      requireEnsure:false, // 禁用require.ensure
      requireContext: false, // 禁用require.context
      browserify: false, // 禁用browerify
      requirejs: false // 禁用requirejs
    }
  }]
}

```
## Resolve
设置webpack如何去寻找依赖的模块

resolve属性 | 示例 | 说明
-|-|-
alias | alias:{commponents: './src/commonents'} |   配置别名来映射导入路径
mainFields | mainFields:['browser', 'main'] | 优先采用多分第三方模块的那块代码
extensions|extensions:['.ts', '.js', '.json']| 导入语句没有后缀时，配置后缀匹配列表
modules|modules: ['../src/components', 'node_modules']|去那些目录寻找第三方模块，默认node_modules
descriptonFiles| descriptonFiles:['package.json'] |配置描述第三方模块的描述，默认package.json
enforceExtension | enforceExtension:true | 是否需要带后缀
enforeModuleExtension | enforeModuleExtension: false |  与enforceExtension相似，但只对node_modules下的模块生效


## Plugins
pligins接收一个数组，数组每项都是一个要使用的plugin的示例，难点在于plugin本身的配置。


## DevServer
用于改变DevServer的默认行为。只有通过DevServer启动Webpack时，配置文件的devserver才生效。

DevServer配置 | 说明 | 示例
-|-|-
hot | 模块热替换功能，不刷新页面，自动实时预览 | 
inline | 若果想实时预览就开启 |
historyApiFallback | 单页面应用针对命中路由返回对应html文件 | historyApiFallback:true, // 都返回index.html<br>historyApiFallback:{rewrites: [{from: '/^/user/', to: '/user.html'},<br>{from: '/^/game/', to: '/game.html}]}
contentBase | DevServer HTTP服务器的文件根目录，默认当前执行目录 | contentBase:path.join(__dirname, 'public')
headers | 在http响应中注入http头 | headers: {'X-for': 'cus'}
host | DevServer服务监听的地址 |
port | DevServer服务监听的端口，默认8080，被占用沿用下一个 |
allowedHosts | 配置白名单列表,只有http请求的host在列表中才能得到正常返回 | allowedHosts:['host.com//单个', '.host2.com//匹配多个']
disableHostCheck | 是否关闭用于DNS重新绑定的http请求的host检查，默认只接受本地请求，通过IP地址访问，需要关闭host检查 |
https | 是否启用https，devServer默认HTTP服务，HTTP2和Service Worker必须运行在https |
clientLogLevel | 客户端日志等级，枚举值none、warnin、info、error取其一，默认info（所有类型日志） |
compress | 是否启用Gzip，默认false | 
open |  浏览器打开开发网页，默认false，devServer.openPage配置指定URL页面 | 


## 其他配置
配置 | 说明
-|-
target | 针对不同运行环境的代码
devtool |  如何生成source map,默认false，开启：devtool: 'source-map'
wath | 支持监听文件更新， 默认关闭
watchOptions | 文件更显监听配置 poll 每秒询问次数
enternals | 不用被打包的模块
resolveLoader |  如何寻找loader


## 示例
```js
const path = require('path');
module.exports = {
  // entry 表示 入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
  // 类型可以是 string | object | array   
  entry: './app/entry', // 只有1个入口，入口只有1个文件
  entry: ['./app/entry1', './app/entry2'], // 只有1个入口，入口有2个文件
  entry: { // 有2个入口
    a: './app/entry-a',
    b: ['./app/entry-b1', './app/entry-b2']
  },

  // 如何输出结果：在 Webpack 经过一系列处理后，如何输出最终想要的代码。
  output: {
    // 输出文件存放的目录，必须是 string 类型的绝对路径。
    path: path.resolve(__dirname, 'dist'),

    // 输出文件的名称
    filename: 'bundle.js', // 完整的名称
    filename: '[name].js', // 当配置了多个 entry 时，通过名称模版为不同的 entry 生成不同的文件名称
    filename: '[chunkhash].js', // 根据文件内容 hash 值生成文件名称，用于浏览器长时间缓存文件

    // 发布到线上的所有资源的 URL 前缀，string 类型
    publicPath: '/assets/', // 放到指定目录下
    publicPath: '', // 放到根目录下
    publicPath: 'https://cdn.example.com/', // 放到 CDN 上去

    // 导出库的名称，string 类型
    // 不填它时，默认输出格式是匿名的立即执行函数
    library: 'MyLibrary',

    // 导出库的类型，枚举类型，默认是 var
    // 可以是 umd | umd2 | commonjs2 | commonjs | amd | this | var | assign | window | global | jsonp ，
    libraryTarget: 'umd', 

    // 是否包含有用的文件路径信息到生成的代码里去，boolean 类型
    pathinfo: true, 

    // 附加 Chunk 的文件名称
    chunkFilename: '[id].js',
    chunkFilename: '[chunkhash].js',

    // JSONP 异步加载资源时的回调函数名称，需要和服务端搭配使用
    jsonpFunction: 'myWebpackJsonp',

    // 生成的 Source Map 文件名称
    sourceMapFilename: '[file].map',

    // 浏览器开发者工具里显示的源码模块名称
    devtoolModuleFilenameTemplate: 'webpack:///[resource-path]',

    // 异步加载跨域的资源时使用的方式
    crossOriginLoading: 'use-credentials',
    crossOriginLoading: 'anonymous',
    crossOriginLoading: false,
  },

  // 配置模块相关
  module: {
    rules: [ // 配置 Loader
      {  
        test: /\.jsx?$/, // 正则匹配命中要使用 Loader 的文件
        include: [ // 只会命中这里面的文件
          path.resolve(__dirname, 'app')
        ],
        exclude: [ // 忽略这里面的文件
          path.resolve(__dirname, 'app/demo-files')
        ],
        use: [ // 使用那些 Loader，有先后次序，从后往前执行
          'style-loader', // 直接使用 Loader 的名称
          {
            loader: 'css-loader',      
            options: { // 给 html-loader 传一些参数
            }
          }
        ]
      },
    ],
    noParse: [ // 不用解析和处理的模块
      /special-library\.js$/  // 用正则匹配
    ],
  },

  // 配置插件
  plugins: [
  ],

  // 配置寻找模块的规则
  resolve: { 
    modules: [ // 寻找模块的根目录，array 类型，默认以 node_modules 为根目录
      'node_modules',
      path.resolve(__dirname, 'app')
    ],
    extensions: ['.js', '.json', '.jsx', '.css'], // 模块的后缀名
    alias: { // 模块别名配置，用于映射模块
       // 把 'module' 映射 'new-module'，同样的 'module/path/file' 也会被映射成 'new-module/path/file'
      'module': 'new-module',
      // 使用结尾符号 $ 后，把 'only-module' 映射成 'new-module'，
      // 但是不像上面的，'module/path/file' 不会被映射成 'new-module/path/file'
      'only-module$': 'new-module', 
    },
    alias: [ // alias 还支持使用数组来更详细的配置
      {
        name: 'module', // 老的模块
        alias: 'new-module', // 新的模块
        // 是否是只映射模块，如果是 true 只有 'module' 会被映射，如果是 false 'module/inner/path' 也会被映射
        onlyModule: true, 
      }
    ],
    symlinks: true, // 是否跟随文件软链接去搜寻模块的路径
    descriptionFiles: ['package.json'], // 模块的描述文件
    mainFields: ['main'], // 模块的描述文件里的描述入口的文件的字段名称
    enforceExtension: false, // 是否强制导入语句必须要写明文件后缀
  },

  // 输出文件性能检查配置
  performance: { 
    hints: 'warning', // 有性能问题时输出警告
    hints: 'error', // 有性能问题时输出错误
    hints: false, // 关闭性能检查
    maxAssetSize: 200000, // 最大文件大小 (单位 bytes)
    maxEntrypointSize: 400000, // 最大入口文件大小 (单位 bytes)
    assetFilter: function(assetFilename) { // 过滤要检查的文件
      return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
    }
  },

  devtool: 'source-map', // 配置 source-map 类型

  context: __dirname, // Webpack 使用的根目录，string 类型必须是绝对路径

  // 配置输出代码的运行环境
  target: 'web', // 浏览器，默认
  target: 'webworker', // WebWorker
  target: 'node', // Node.js，使用 `require` 语句加载 Chunk 代码
  target: 'async-node', // Node.js，异步加载 Chunk 代码
  target: 'node-webkit', // nw.js
  target: 'electron-main', // electron, 主线程
  target: 'electron-renderer', // electron, 渲染线程

  externals: { // 使用来自 JavaScript 运行环境提供的全局变量
    jquery: 'jQuery'
  },

  stats: { // 控制台输出日志控制
    assets: true,
    colors: true,
    errors: true,
    errorDetails: true,
    hash: true,
  },

  devServer: { // DevServer 相关的配置
    proxy: { // 代理到后端服务接口
      '/api': 'http://localhost:3000'
    },
    contentBase: path.join(__dirname, 'public'), // 配置 DevServer HTTP 服务器的文件根目录
    compress: true, // 是否开启 gzip 压缩
    historyApiFallback: true, // 是否开发 HTML5 History API 网页
    hot: true, // 是否开启模块热替换功能
    https: false, // 是否开启 HTTPS 模式
    },

    profile: true, // 是否捕捉 Webpack 构建的性能信息，用于分析什么原因导致构建性能不佳

    cache: false, // 是否启用缓存提升构建速度

    watch: true, // 是否开始
    watchOptions: { // 监听模式选项
    // 不监听的文件或文件夹，支持正则匹配。默认为空
    ignored: /node_modules/,
    // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
    // 默认为300ms 
    aggregateTimeout: 300,
    // 判断文件是否发生变化是不停的去询问系统指定文件有没有变化，默认每隔1000毫秒询问一次
    poll: 1000
  },
}

```


## 项目中常用
### babel-loader

### 使用ts

### 使用scss

### jsx语法

### 构建Electron应用

### 多个单页面应用


