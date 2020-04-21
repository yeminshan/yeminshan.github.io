---
title: vue渲染函数&jsx
date: 2020-04-10 19:59:48
tags:   [vue, jsx]
categories: [vue, jsx]
top: true
---

# Vue渲染函数 & jsx 


## vue关于渲染几个概念理解
### 1.节点
### 2.树[DOM树]
### 3.虚拟节点Vnode


## 渲染函数
createEleme方法实质是创建节点描述 [createNodeDescription]

```js
// @returns {VNode} 虚拟节点
createElement(
  // 参数1
  // {String | Object | Function}
  // 一个 HTML 标签字符串，组件选项对象，或者
  // 解析上述任何一种的一个 async 异步函数，必要参数。

  'div',
  // 参数2
  // {Object}
  // 一个包含模板相关属性的数据对象
  // 这样，您可以在 template 中使用这些属性。可选参数。
  {
    // (详情见下一节)
  },

  // 参数3
  // {String | Array}
  // 子节点 (VNodes)，由 `createElement()` 构建而成，
  // 或使用字符串来生成“文本节点”。可选参数。
  [
    '先写一些文字',
    createElement('h1', '一则头条'),
    createElement(MyComponent, {
      props: {
        someProp: 'foobar'
      }
    })
  ]
) 
```

数据对象
```js
{
  // 和`v-bind:class`一样的 API
  // 接收一个字符串、对象或字符串和对象组成的数组
  'class': {
    foo: true,
    bar: false
  },
  // 和`v-bind:style`一样的 API
  // 接收一个字符串、对象或对象组成的数组
  style: {
    color: 'red',
    fontSize: '14px'
  },
  // 正常的 HTML 特性
  attrs: {
    id: 'foo'
  },
  // 组件 props
  props: {
    myProp: 'bar'
  },
  // DOM 属性
  domProps: {
    innerHTML: 'baz'
  },
  // 事件监听器基于 `on`
  // 所以不再支持如 `v-on:keyup.enter` 修饰器
  // 需要手动匹配 keyCode。
  on: {
    click: this.clickHandler
  },
  // 仅对于组件，用于监听原生事件，而不是组件内部使用
  // `vm.$emit` 触发的事件。
  nativeOn: {
    click: this.nativeClickHandler
  },
  // 自定义指令。注意，你无法对 `binding` 中的 `oldValue`
  // 赋值，因为 Vue 已经自动为你进行了同步。
  directives: [
    {
      name: 'my-custom-directive',
      value: '2',
      expression: '1 + 1',
      arg: 'foo',
      modifiers: {
        bar: true
      }
    }
  ],
  // 作用域插槽格式
  // { name: props => VNode | Array<VNode> }
  scopedSlots: {
    default: props => createElement('span', props.text)
  },
  // 如果组件是其他组件的子组件，需为插槽指定名称
  slot: 'name-of-slot',
  // 其他特殊顶层属性
  key: 'myKey',
  ref: 'myRef'
}
```


## jsx写法
React发明了JSX，利用HTML语法来创建虚拟DOM。当遇到 "<"，JSX就当HTML解析，遇到 "{" 就当JavaScript解析。 因此用一般模板写变量用 "{}",中间是javascript表达式。特别注意render里面的指向问题！在组件里面使用也要带this的。

[JSX 如何映射到 JavaScript](https://github.com/vuejs/jsx#installation)

### vue如何支持jsx写法
在Vue 文件中引入 jsx 文件！ 需要配置webpack，令其支持处理jsx文件
```js
{// 对src和test文件夹下的.js文件使用babel-loader将es6+的代码转成es5
test: /\.(js|jsx)$/,
loader:'babel-loader',
exclude: /node_modules/,
include: [resolve('src'), resolve('test'), resolve('node_modules/webpack-dev-server/client')],
use: {
    loader:'babel-loader?cacheDirectory=true'
  }
},
```
### 1.基础使用
```js
vue.component('jsxDemo', {
  render(h) {
    return (<p>jsxdemo</p>)
  }
})
```
### 2.v-if / v-else
```js
// 三元表达式写法
vue.component('jsxDemo', {
  render(h) {
    return (<p>{a=== 1 ? 'a' : 'b'}</p>)
  }
})
// 复杂的
vue.component('jsxDemo', {
  render(h) {
    let text
    if a === 1 text = a
    else if a === 2 text = b
    else text = c
    return(
        <p>{text}</p>
    )
  }
})
```

### 3.v-for （渲染重复的样式,转化为数组）
```js
vue.component('jsxDemo', {
  render(h) {
    let arr = [a, b, c]
    return (
        <div>
            this.arr.map(i => (<p>{i}</p>))
        </div>
    )
  }
})
```


### 4.v-model 语法糖(即传值value+事件绑定input监听）)
```js
vue.component('jsxDemo', {
data() {
    return {
        text: ''
    }
}
methods:{
    input(e){
        this.text=e.target.value
    }
},
render(){
    return (
        <div>
            <input type="text" value={this.text} onInput={this.input}/>
            <p>{this.text}</p>
        </div>
    )
}
})
```

### 5.自定义组件 直接引入使用
```js
import HelloWolrd from './HelloWorld'
vue.component('jsxDemo', {
  render(h) {
    return (
      <HelloWolrd/>
    )
  }
})
```

### 6.事件 (native)on-事件 
```js
vue.component('jsxDemo', {
  render(h) {
    return (
        <el-button onClick={functionName} nativeOnClick={functionName} on-click="functionName"></el-button>
    )
  }
})
```

### 7. class， style 
```js
vue.component('jsxDemo', {
render() {
    return (
        <div class={{XX:XX}} class={["XX1", {"XXX3": true}]} style={{XXX: XX}}></div>
    )
  }
})
```
###  8.JSX中的函数式组件
[jsx函数式组件](https://juejin.im/post/5affa64df265da0b93488fdd#heading-9 "参考资料")
```js
//父组件
// context.data就是你在父组件给子组件增加的属性
//  ...省略无关代码
 render(){
      return (
        <Item data={this.data} class="large"/>
      )
    }
//Item.vue组件
export default {
    functional:true,
      name: "item",
      render(h,context){
        return (
          <div class="red" {...context.data}>
            {context.props.data}
          </div>
        )
      }
    }
```

### 9.slot 之深度复制
```js
function deepClone(vnodes, createElement) {

  function cloneVNode (vnode) {
    const clonedChildren = vnode.children && vnode.children.map(vnode => cloneVNode(vnode));
    const cloned = createElement(vnode.tag, vnode.data, clonedChildren);
    cloned.text = vnode.text;
    cloned.isComment = vnode.isComment;
    cloned.componentOptions = vnode.componentOptions;
    cloned.elm = vnode.elm;
    cloned.context = vnode.context;
    cloned.ns = vnode.ns;
    cloned.isStatic = vnode.isStatic;
    cloned.key = vnode.key;

    return cloned;
  }

  const clonedVNodes = vnodes.map(vnode => cloneVNode(vnode))
  return clonedVNodes;
}
```

最后 还是官方文档极度舒适
[vue/jsx](https://github.com/vuejs/jsx#installation)