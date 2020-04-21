---
title: 深入了解vue
date: 2020-04-12 16:51:46
tags: [vue]
categories: [vue]
---


1. vue 工作机制

new Vue 

初始化 （生命周期和事件）

beforeCreate

初始化 （注入和校验）

created

编译阶段
1。指定el ，到3，
2。未指定el 当调用vm.$mount(e) 到3
3。 指定template，是：template编译到render函数中，否： 将el外部的html作为template编译


beforeMoint
创建vm.$el 替换el

mounted （挂载完毕）

当有更新 虚拟DOM重新渲染，并应用更新

调用vm.destory()

beforeDestroy
解除绑定销毁子组件以及事件监听器
销毁完毕
...
destroyed




2. 响应式原理
全靠Object.defineProperty（ES6，ES5无法shim的特性，因此不支持IE8及以下）,转换成getter/setter

```js
constructor(options) { 
  this._data = options.data; 
  this.observer(this._data); 
  }
  observer(value) {
    if (!value || typeof value !== "object") { return;
     }
     Object.keys(value).forEach(key => { 
       this.defineReactive(value, key, value[key]); 
       });
      }
     defineReactive(obj, key, val) { 
       Object.defineProperty(obj, key, {
         enumerable: true /* 属性可枚举 */, 
         configurable: true /* 属性可被修改或删除 */,
          get() { return val; },
          set(newVal) { 
            if (newVal === val) return;
            this.cb(newVal);
          }
       }
     }
  }
}
```