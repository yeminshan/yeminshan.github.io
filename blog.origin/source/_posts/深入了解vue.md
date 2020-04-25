---
title: 深入了解vue
date: 2020-04-12 16:51:46
tags: [vue]
categories: [vue]
---
[参考1](https://ustbhuangyi.github.io/vue-analysis/v2/prepare/)

1. vue 工作机制

上图一张
[资料:Vue的内部运行机制](https://blog.csdn.net/RedLoVE0908/article/details/99749359)
![avatar](https://img-blog.csdnimg.cn/20190819185138874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlZExvVkUwOTA4,size_16,color_FFFFFF,t_70)
![avatar](https://cn.vuejs.org/images/lifecycle.png)



2. 响应式原理实现
全靠Object.defineProperty（ES6，ES5无法shim的特性，因此不支持IE8及以下）,转换成getter/setter


Object.defineProperty(obj, prop, descriptor)
直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

参数 | 描述
-|-
obj | 要定义属性的对象。
prop | 要定义或修改的属性的名称或 Symbol 。
descriptor | 要定义或修改的属性描述符。



descriptor属性 | 说明
-|-
configurable | 该属性的描述符才能够被改变，同时该属性也能从对应的对象上被删除。默认为 false。
enumerable | 当且仅当该属性的 enumerable 键值为 true 时，该属性才会出现在对象的枚举属性中。默认为 false。
value | 该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。默认为 undefined。
writable | 当且仅当该属性的 writable 键值为 true 时，属性的值，也就是上面的 value，才能被赋值运算符改变。默认为 false。
get |  属性的 getter 函数，如果没有 getter，则为 undefined。当访问该属性时，会调用此函数。执行时不传入任何参数，但是会传入 this 对象（由于继承关系，这里的this并不一定是定义该属性的对象）。该函数的返回值会被用作属性的值。默认为 undefined。
set |  属性的 setter 函数，如果没有 setter，则为 undefined。当属性值被修改时，会调用此函数。该方法接受一个参数（也就是被赋予的新值），会传入赋值时的 this 对象。
默认为 undefined。


```js
constructor(options) { 
  this._data = options.data; 
  this.observer(this._data); 
  }
  observer(value) {
    if (!value || typeof value !== "object") { return;
     }
    //  对于data 逐个设置getter和setter
     Object.keys(value).forEach(key => { 
       this.defineReactive(value, key, value[key]); 
       });
      }
     defineReactive(obj, key, val) { 
      //  
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

3. 依赖收集与追踪

![avatar](https://img-blog.csdnimg.cn/20190819190253771.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1JlZExvVkUwOTA4,size_16,color_FFFFFF,t_70)

在get中执行dep.depend()进行依赖绑定
在set中执行dep.notify()通知观察者进行视图更新
nextTick的异步更新策略
在响应式更新数据的过程中，如果一个数据的值在一段时间内频繁更新了很多次，会依次触发响应式setter->Dep->Watcher->update->patch,所以引入nextTick的异步更新策略，实现一个queue队列，会在下一个tick去执行一次上面的响应式更新操作，大大优化了性能。

使用到nextTick 的场景：
$nextTick(callback)。这里的回调函数（callback）将在数据更新完成，视图更新完毕之后被调用。

1.Vue生命周期的created()钩子函数进行的DOM操作一定要放在Vue.nextTick()的回调函数中.

原因： created()钩子函数执行的时候DOM 其实并未进行任何渲染，而此时进行DOM操作无异于徒劳，所以此处一定要将DOM操作的js代码放进Vue.nextTick()的回调函数中。
2.在数据变化后要执行的某个操作，而这个操作【需要使用随数据改变而改变的DOM结构的时候】，这个操作都应该放进Vue.nextTick()的回调函数中。更新数据后，想要使用js对新的视图进行操作时
3.在使用某些第三方插件时 ，这些插件需要【dom动态变化后重新应用该插件】，这时候就需要使用$nextTick()来重新应用插件的方法。

4. 编译
1 parse：（解析）利用正则将模板转换成抽象语法树（AST）；
2 optimize： （标记静态节点做优化）标记静态节点，以后update的时候，diff算法可以跳过静态节点
3 generate：（转成字符串）将抽象语法树（AST）转成字符串，供render去渲染DOM

render function被渲染的时候，会对data对象进行数据读取，会触发getter函数，从而把data里面的属性进行依赖收集，依赖收集的目的是将这些属性放到观察者（Watcher）的观察队列中，一旦我们对data里面的属性进行修改时，就会触发setter函数，setter告诉观察者数据变化，需要重新渲染视图，观察者调用update来更新视图。

在update的时候，会执行【patch】，将新旧VNode传进去，通过diff算法算出差异，局部更新视图，做到最优化。
