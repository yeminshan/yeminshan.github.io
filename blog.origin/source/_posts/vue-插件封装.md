---
title: vue 插件封装
date: 2020-04-21 16:08:02
tags: [vue]
categories: [vue]
---

## 简述

1. 使用必须在 new Vue 前，引入插件并使用 Vue.use(插件名， { // 参数})
2. 开发封装：

必须暴露 install 方法

必须暴露出去 export default XXX

## 开发插件的几种方法

```js
// @params:Vue Vue构造器
// @params：options {Object} 对象-可选参数

插件名.install = function(Vue, options) {
    // 0. 全局注册组件 (组件名，组件)
      Vue.component('PageGrid', PageGrid);

    // 1. 添加【全局方法或属性】 vue-custom-element 【 Vue.全局方法名| 属性名】
      Vue.myGlobalMethod = function () {
        // 逻辑...
       }

    // 2. 添加全局资源 指令/过滤器/过渡等，如 vue-touch
      Vue.directive('my-directive', {
        bind (el, binding, vnode, oldVnode) {
          // 逻辑...
        }
        ...
      })

    // 3. 注入组件选项   通过全局 mixin方法添加一些组件选项，如: vuex 【某个钩子 mixins，一些内容 】
      Vue.mixin({
        created: function () {
          // 逻辑...
        }
        ...
      })

    // 4. 添加【实例方法】   通过把它们添加到 Vue.prototype.方法名(一般用$区分开) 上实现
      Vue.prototype.$myMethod = function (methodOptions) {
        // 逻辑...
      }
}

```


Eg
```js
function install(Vue) {
  if (install.installed) return;
  Vue.component('Grid', Grid)
}

export default {
  install
}

```
