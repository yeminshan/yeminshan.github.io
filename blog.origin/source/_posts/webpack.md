---

title: webpack简单认识
date: 2020-03-21 15:34:51
tags: [webpack]
top: true
---

### 模块化演进

模块化发展： 

1. 文件划分：

每个功能及其相关状态数据各自单独放到不同的 JS 文件中，约定每个文件是一个独立的模块。使用某个模块将这个模块引入到页面中，一个 script 标签对应一个模块，然后直接调用模块中的成员（变量 / 函数）。

如 a.js b.js。

缺点：

模块直接在全局工作，大量模块成员污染全局作用域；
没有私有空间，所有模块内的成员都可以在模块外部被访问或者修改；
一旦模块增多，容易产生命名冲突；
无法管理模块与模块之间的依赖关系；
在维护的过程中也很难分辨每个成员所属的模块。

2. 命名空间方式：

   每个模块只暴露一个全局对象，所有模块成员都挂载到这个全局对象中，具体做法是在第一阶段的基础上，通过将每个模块“包裹”为一个全局对象的形式实现，这种方式就好像是为模块内的成员添加了“命名空间”，所以我们又称之为命名空间方式。

   如：

   ```
   moduleA = {
   method1: function () {
       console.log('moduleA#method1')
    }}
    
   moduleA.method1()
   ```

   

3.IIFE (立即执行函数):

每个模块成员都放在立即执行函数的私有作用域中，需要暴露给外部成员通过挂载到全局对象上。

这种方式带来了私有成员的概念，私有成员只能在模块成员内通过闭包的形式访问，这就解决了前面所提到的全局作用域污染和命名冲突的问题。

如

```js
(function () {
let public = 'moduleA',
const private = 'modulePrivate'
function methods () {
return public
}
window.moduleA = {
	methods: methods
}
})()
```

4. IIFE 依赖参数

   ```js
   (function ($) {
   let public = 'moduleA',
   const private = 'modulePrivate'
   function methods () {
   	return public
   }
   window.moduleA = {
   	methods: methods
   }
   })(jQuery)
   ```

   ### 模块化规范的出现

#### 1. commonjsJs（同步加载模块）

广泛使用的 javascript 模块化规范，node.js 采用这种方式得以流行。
缺点：不适合直接运行在浏览器环境下

moduel.exports 导出

require 函数导入

#### 2. AMD（Asynchronous Module Definition 异步模块定义规范）

异步加载依赖模块，可直接在浏览器运行，但是 javascript 运行环境没有原生支持，需要倒入 AMD 库



**在 Node.js 环境中，我们遵循 CommonJS 规范来组织模块。**（CommonJS 属于内置模块系统）
**在浏览器环境中，我们遵循 ES Modules 规范。**

Node 环境也会逐渐趋向于 ES Modules 规范。语言层面实现的模块化。

#### ES6 模块化

语言层面上实现模块化，浏览器和 nodejs 支持改规范

#### 样式文件的模块化如 SCSS

### 新语言

#### ES6 js 下一代标准

#### Typescript

js 超集，可被编译成 ES5，ES6 标准，支持 ES6 所有功能和静态类型检查。缺点：无法直接在浏览器和 node 环境运行

#### Flow

js 超集，比 ts 灵活

### 构建

构建的主要工作：代码转换，文件优化，代码分割，模块合并，自动刷新，代码校验，自动发布。

#### 常用构建工具

##### Npm Script

任务执行者，是 npm 内置功能，通过 package.json 中 scripts 定义任务。底层通过调用 shell 运行脚本。无需安装其他依赖，缺点：太简单。

##### Grunt

进化版的 npm script，集成度不高，无法开箱即用

##### Glup

基于流自动化构建工具，灵活好用，可单独构建，亦可搭配其他工具使用。缺点和 Grunt 一致。

##### Fis3

百度优秀国产构建工具，功能强大，集成各种 web 所需构建功能，但是官方不再维护且不支持最新版本 nodejs

##### webpack

专注于构建模块化项目，缺点只能用于模块化项目。

##### Rollup

类似 webpack，但不如 webpack 完善

# Webpack

解决的问题： 在项目中更高效地管理和维护项目中的每个资源。

1. 具备编译代码的能力，新特性的代码转换为能够兼容大多数环境的代码，解决我们所面临的环境兼容问题
2. 散落的模块再打包到一起，这样就解决了浏览器频繁请求模块文件的问题。开发阶段才需要模块化的文件划分!
3. 支持不同种类的前端模块类型,样式、图片、字体等所有资源文件都作为模块使用

核心：loader 机制和插件机制

## 安装

    npm i -D webpack@<version>
    npm i --save-dev webpack@<version>
    npm i -g webpack // 全局

非全局安装成功后可以一下命令查看版本

    node_modules/webpack/bin/webpack.js -v
    node_modules/.bin/webpack -v // 安装后bin文件夹里会多了一个webpack可执行文件的软链

## 使用

webpack 执行构建，如果根目录存在 webpack.config.js，默认会读取里面的配置。注意配置至少定义入口文件和输出文件。没有配置 webpack.config.js 文件也可执行。

    node_modules/webpack/bin/webpack.js <入口文件名> <输出文件名>
    node_modules/.bin/webpack <入口文件名> <输出文件名>

此时生成打包后文件会出现在根目录下。

## webpack 核心概念

webpack 从 entry 递归解析出所有依赖的 module，根据配置的 loader 进行转换，安装 Entry 进行分组，每个分组一个 Chunk，最后将 chunk 转换成文件输出

1.  Entry
    入口，webpack 构建的第一步。

2.  Modules
    模块，webpack 中一切皆可视为模块，webpack 会从入口递归找出所有依赖模块。配置处理模块的规则，

3.  Chunk
    代码块，用于代码分割合并，一个 chunk 可以由多个模块生成。

4.  Loader
    转换器

5.  Plugin
    扩展插件

6.  Output
    输出结果

## 配置

配置 webpack 方式：

1. 通过 js 描述文件，如：webpack.config.js
2. webpack 可执行文件命令行传参 如：webpack--devtool source-map

### Entry

配置模块入口，必填。
入口文件的路径可以是**相对路径**，webpack 寻找相对路径文件以 context 为根目录，context 默认值为执行启动 webpack 时所在的当前工作目录，且 context 必须是一个绝对路径的字符串。

修改 context 方式：

1. 配置文件设置

   module.export = {
   context: path.resolve(\_\_dirname, 'app')
   }

2. 启动命令带参

   webpack --context XXX

| 类型           | 含义                                                           | 示例                                                      |
| -------------- | -------------------------------------------------------------- | --------------------------------------------------------- |
| Sting          | 单一入口，输出 Chunk 名称为 main                               | entry：'index.js'                                         |
| Array\<String> | 单一入口，仅会输出最后一个 Chunk，名称为 main                  | entry: ['index.js', 'home.js']                            |
| Object         | 多入口,每个入口生成一个 Chunk，Chunk 名称为 Object‘ s key name | entry: {<br> index: 'index.js',<br> home: 'home.js'<br> } |

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

### Output

内资变量表

| 变量名    | 含义                                             |
| --------- | ------------------------------------------------ |
| id        | chunk 唯一标识，从 0 开始                        |
| name      | chunk 名称                                       |
| hash      | chunk 唯一标识 hash 值,长度可指定，默认[hash:20] |
| chunkhash | chunk 内容的 hash,长度可指定，默认[hash:20]      |

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

| output 其他常用属性 | 可选值                                                                                                                                                           | 说明                                                                      |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| crossOriginLoading  | anonymous,默认值。加载脚本不会带上用户的 Cookies<br>use-credentials 会带上 Cookies                                                                               | jsonp 原理引入 script 加载异步资源，用于设置异步引入标签的 crossorigin 值 |
| library             | 字符串                                                                                                                                                           | 导出库的名称，常与 libraryTarget 一起使用                                 |
| libraryTarget       | 字符串枚举值，['var', 'commonjs', 'commonjs2', 'this', 'window', 'global']详情见[官网](https://webpack.docschina.org/configuration/output/#output-librarytarget) | 何种方式导出库                                                            |
| libraryExport       |                                                                                                                                                                  | 只有在 libraryTarget 设置为 commonjs 和 commonjs2 才有意义                |

## Module

| Module 属性 | 类型                     | 说明                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| noParse     | RegExp,[RegExp],function | 设置让 webpack 忽略的无需模块化的文件，且被忽略文件不应该有模块化语句，如 require，import，define                                                                                                                                                                                                                                                                                    |
| rules       | Array                    | 创建模块时，匹配请求的规则数组。这些规则能够修改模块的创建方式。这些规则能够对模块(module)应用 loader，或者修改解析器(parser)。<br>每个规则可以分为三部分 - 条件(condition)，结果(result)和嵌套规则(nested rule)<br>test、include、exclude 选中应用 loader 的文件，指出字符串，正则，数组<br>use 设置应用 loader 数组，默认从右往左执行。enfore 设置执行顺序的最前 pre 和最后 post。 |

webpack 内置了对 js 的解析功能，parser 支持更细粒度地配置模块语法解析，其与 noParse 区别在于，前者精确到语法层面，后者只控制到文件。

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
  },
  {
      test: /\.css$/,
      use: [ 'style-loader','css-loader'], // 先安装 npm install css-loader --save-dev  Loader，执行顺序是从后往前执行的!!!!!!
   }]
}

```

## Resolve

设置 webpack 如何去寻找依赖的模块

| resolve 属性          | 示例                                           | 说明                                                       |      |
| --------------------- | ---------------------------------------------- | ---------------------------------------------------------- | ---- |
| alias                 | alias:{commponents: './src/commonents'}        | 配置别名来映射导入路径                                     |      |
| mainFields            | mainFields:['browser', 'main']                 | 优先采用多分第三方模块的那块代码                           |      |
| extensions            | extensions:['.ts', '.js', '.json']             | 导入语句没有后缀时，配置后缀匹配列表                       |      |
| modules               | modules: ['../src/components', 'node_modules'] | 去那些目录寻找第三方模块，默认 node_modules                |      |
| descriptonFiles       | descriptonFiles:['package.json']               | 配置描述第三方模块的描述，默认 package.json                |      |
| enforceExtension      | enforceExtension:true                          | 是否需要带后缀                                             |      |
| enforeModuleExtension | enforeModuleExtension: false                   | 与 enforceExtension 相似，但只对 node_modules 下的模块生效 |      |

## Plugins

pligins 接收一个数组，数组每项都是一个要使用的 plugin 的示例，难点在于 plugin 本身的配置。

## DevServer

用于改变 DevServer 的默认行为。只有通过 DevServer 启动 Webpack 时，配置文件的 devserver 才生效。

| DevServer 配置     | 说明                                                                                                        | 示例                                                                                                                                                           |
| ------------------ | ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hot                | 模块热替换功能，不刷新页面，自动实时预览                                                                    |
| inline             | 若果想实时预览就开启                                                                                        |
| historyApiFallback | 单页面应用针对命中路由返回对应 html 文件                                                                    | historyApiFallback:true, // 都返回 index.html<br>historyApiFallback:{rewrites: [{from: '/^/user/', to: '/user.html'},<br>{from: '/^/game/', to: '/game.html}]} |
| contentBase        | DevServer HTTP 服务器的文件根目录，默认当前执行目录                                                         | contentBase:path.join(\_\_dirname, 'public')                                                                                                                   |
| headers            | 在 http 响应中注入 http 头                                                                                  | headers: {'X-for': 'cus'}                                                                                                                                      |
| host               | DevServer 服务监听的地址                                                                                    |
| port               | DevServer 服务监听的端口，默认 8080，被占用沿用下一个                                                       |
| allowedHosts       | 配置白名单列表,只有 http 请求的 host 在列表中才能得到正常返回                                               | allowedHosts:['host.com//单个', '.host2.com//匹配多个']                                                                                                        |
| disableHostCheck   | 是否关闭用于 DNS 重新绑定的 http 请求的 host 检查，默认只接受本地请求，通过 IP 地址访问，需要关闭 host 检查 |
| https              | 是否启用 https，devServer 默认 HTTP 服务，HTTP2 和 Service Worker 必须运行在 https                          |
| clientLogLevel     | 客户端日志等级，枚举值 none、warnin、info、error 取其一，默认 info（所有类型日志）                          |
| compress           | 是否启用 Gzip，默认 false                                                                                   |
| open               | 浏览器打开开发网页，默认 false，devServer.openPage 配置指定 URL 页面                                        |

## 其他配置

| 配置          | 说明                                                        |
| ------------- | ----------------------------------------------------------- |
| target        | 针对不同运行环境的代码                                      |
| devtool       | 如何生成 source map,默认 false，开启：devtool: 'source-map' |
| wath          | 支持监听文件更新， 默认关闭                                 |
| watchOptions  | 文件更显监听配置 poll 每秒询问次数                          |
| enternals     | 不用被打包的模块                                            |
| resolveLoader | 如何寻找 loader                                             |

## 示例

```js
const path = require("path");
module.exports = {
  // entry 表示 入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
  // 类型可以是 string | object | array
  entry: "./app/entry", // 只有1个入口，入口只有1个文件
  entry: ["./app/entry1", "./app/entry2"], // 只有1个入口，入口有2个文件
  entry: {
    // 有2个入口
    a: "./app/entry-a",
    b: ["./app/entry-b1", "./app/entry-b2"],
  },

  // 如何输出结果：在 Webpack 经过一系列处理后，如何输出最终想要的代码。
  output: {
    // 输出文件存放的目录，必须是 string 类型的绝对路径。
    path: path.resolve(__dirname, "dist"),

    // 输出文件的名称
    filename: "bundle.js", // 完整的名称
    filename: "[name].js", // 当配置了多个 entry 时，通过名称模版为不同的 entry 生成不同的文件名称
    filename: "[chunkhash].js", // 根据文件内容 hash 值生成文件名称，用于浏览器长时间缓存文件

    // 发布到线上的所有资源的 URL 前缀，string 类型
    publicPath: "/assets/", // 放到指定目录下
    publicPath: "", // 放到根目录下
    publicPath: "https://cdn.example.com/", // 放到 CDN 上去

    // 导出库的名称，string 类型
    // 不填它时，默认输出格式是匿名的立即执行函数
    library: "MyLibrary",

    // 导出库的类型，枚举类型，默认是 var
    // 可以是 umd | umd2 | commonjs2 | commonjs | amd | this | var | assign | window | global | jsonp ，
    libraryTarget: "umd",

    // 是否包含有用的文件路径信息到生成的代码里去，boolean 类型
    pathinfo: true,

    // 附加 Chunk 的文件名称
    chunkFilename: "[id].js",
    chunkFilename: "[chunkhash].js",

    // JSONP 异步加载资源时的回调函数名称，需要和服务端搭配使用
    jsonpFunction: "myWebpackJsonp",

    // 生成的 Source Map 文件名称
    sourceMapFilename: "[file].map",

    // 浏览器开发者工具里显示的源码模块名称
    devtoolModuleFilenameTemplate: "webpack:///[resource-path]",

    // 异步加载跨域的资源时使用的方式
    crossOriginLoading: "use-credentials",
    crossOriginLoading: "anonymous",
    crossOriginLoading: false,
  },

  // 配置模块相关
  module: {
    rules: [
      // 配置 Loader
      {
        test: /\.jsx?$/, // 正则匹配命中要使用 Loader 的文件
        include: [
          // 只会命中这里面的文件
          path.resolve(__dirname, "app"),
        ],
        exclude: [
          // 忽略这里面的文件
          path.resolve(__dirname, "app/demo-files"),
        ],
        use: [
          // 使用那些 Loader，有先后次序，从后往前执行
          "style-loader", // 直接使用 Loader 的名称
          {
            loader: "css-loader",
            options: {
              // 给 html-loader 传一些参数
            },
          },
        ],
      },
    ],
    noParse: [
      // 不用解析和处理的模块
      /special-library\.js$/, // 用正则匹配
    ],
  },

  // 配置插件
  plugins: [],

  // 配置寻找模块的规则
  resolve: {
    modules: [
      // 寻找模块的根目录，array 类型，默认以 node_modules 为根目录
      "node_modules",
      path.resolve(__dirname, "app"),
    ],
    extensions: [".js", ".json", ".jsx", ".css"], // 模块的后缀名
    alias: {
      // 模块别名配置，用于映射模块
      // 把 'module' 映射 'new-module'，同样的 'module/path/file' 也会被映射成 'new-module/path/file'
      module: "new-module",
      // 使用结尾符号 $ 后，把 'only-module' 映射成 'new-module'，
      // 但是不像上面的，'module/path/file' 不会被映射成 'new-module/path/file'
      "only-module$": "new-module",
    },
    alias: [
      // alias 还支持使用数组来更详细的配置
      {
        name: "module", // 老的模块
        alias: "new-module", // 新的模块
        // 是否是只映射模块，如果是 true 只有 'module' 会被映射，如果是 false 'module/inner/path' 也会被映射
        onlyModule: true,
      },
    ],
    symlinks: true, // 是否跟随文件软链接去搜寻模块的路径
    descriptionFiles: ["package.json"], // 模块的描述文件
    mainFields: ["main"], // 模块的描述文件里的描述入口的文件的字段名称
    enforceExtension: false, // 是否强制导入语句必须要写明文件后缀
  },

  // 输出文件性能检查配置
  performance: {
    hints: "warning", // 有性能问题时输出警告
    hints: "error", // 有性能问题时输出错误
    hints: false, // 关闭性能检查
    maxAssetSize: 200000, // 最大文件大小 (单位 bytes)
    maxEntrypointSize: 400000, // 最大入口文件大小 (单位 bytes)
    assetFilter: function (assetFilename) {
      // 过滤要检查的文件
      return assetFilename.endsWith(".css") || assetFilename.endsWith(".js");
    },
  },

  devtool: "source-map", // 配置 source-map 类型

  context: __dirname, // Webpack 使用的根目录，string 类型必须是绝对路径

  // 配置输出代码的运行环境
  target: "web", // 浏览器，默认
  target: "webworker", // WebWorker
  target: "node", // Node.js，使用 `require` 语句加载 Chunk 代码
  target: "async-node", // Node.js，异步加载 Chunk 代码
  target: "node-webkit", // nw.js
  target: "electron-main", // electron, 主线程
  target: "electron-renderer", // electron, 渲染线程

  externals: {
    // 使用来自 JavaScript 运行环境提供的全局变量
    jquery: "jQuery",
  },

  stats: {
    // 控制台输出日志控制
    assets: true,
    colors: true,
    errors: true,
    errorDetails: true,
    hash: true,
  },

  devServer: {
    // DevServer 相关的配置
    proxy: {
      // 代理到后端服务接口
      "/api": "http://localhost:3000",
    },
    contentBase: path.join(__dirname, "public"), // 配置 DevServer HTTP 服务器的文件根目录
    compress: true, // 是否开启 gzip 压缩
    historyApiFallback: true, // 是否开发 HTML5 History API 网页
    hot: true, // 是否开启模块热替换功能
    https: false, // 是否开启 HTTPS 模式
  },

  profile: true, // 是否捕捉 Webpack 构建的性能信息，用于分析什么原因导致构建性能不佳

  cache: false, // 是否启用缓存提升构建速度

  watch: true, // 是否开始
  watchOptions: {
    // 监听模式选项
    // 不监听的文件或文件夹，支持正则匹配。默认为空
    ignored: /node_modules/,
    // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
    // 默认为300ms
    aggregateTimeout: 300,
    // 判断文件是否发生变化是不停的去询问系统指定文件有没有变化，默认每隔1000毫秒询问一次
    poll: 1000,
  },
};
```

## 项目中常用

### babel-loader

### 使用 ts

### 使用 scss

### jsx 语法

### 构建 Electron 应用

### 多个单页面应用























```
$ npm init --yes
$ npm i webpack webpack-cli --save-dev

$ npx webpack --version
// @TODO !!!!!
 webpack智能提示 
```

