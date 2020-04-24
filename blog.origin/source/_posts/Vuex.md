---
title: Vuex
date: 2020-04-12 14:52:23
tags: [vue, vuex]
categories:
---

状态管理：即共享数据管理
核心概念
1. store
2. state // 数据中心
3. mutations // 操作数据
4. Actions // 触发操作，执行动作，改变数据

状态管理模式

0.安装
1.vuex 依赖Promise ，必要时npm install es6-promise --save

1.概念
1.1定义 ： 专为vue.js应用程序开发的状态管理模式。集中式存储管理应用的所有组件的状态【共享的变量=>全局变量】,并以相应的规则保证状态以一种可预测的方式发生变化【方法统一】。

1.2 应用场景： 单页面应用不同组件的数据流通。【vuex state存放的数据是当前页面初始化生成的一个实例。刷新页面数据重新生成】

对于简单的应用，使用 global event bus 即可。

1.3：基础概念：

1.3.0 store 仓库，实质是一个容器。
```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```



1.3.1 state  单一状态树 【实质是一个json对象？而且是应用唯一的】,响应式的.在计算属性返回即可.

1.3.2 getters 对state中数据的一种过滤，加强属性。

1.3.3 actions 对于state数据的修改动作在这里提交，提交即触发mutation修改数据

1.3.4 mutations 更改vuex的store中的状态的唯一方法 ，只能执行同步操作。获取状态兑现： state= $store.state ，store .commit触发状态变化.[改变变量必须要用提交mution方法,不能直接修改state里的值]

1.3.5 module 模块化 

 

2.核心（统一在store.js里面设置）
2.1 state 

注意: 并不是所有的状态都放到vuex中进行管理,严格于单个组件的状态应作为组件的局部状态.

获取store.js的state值赋给模板的data值：
2.1.1获取vuex的状态

 计算属性返回
 ```
 const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}
 ```

注意: 1.众组件频繁导入store. 解决方法: 在根实例注册store选项,这样子组件就可以用this.$store访问

 

2.1.2 mapState 辅助函数

当获取多个状态时,逐个声明为计算属性先得重复冗余

解决方法:

mapState 辅助生成计算属性,返回的是一个对象. 【需要先import】
```
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'
export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,
    // 计算属性的名称和state子节点名称相同, 可简写
    // 映射 this.count 为 store.state.count
    //'count'
    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',
    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```

 

 

2.1.3 对象展开运算符

将mapState 返回的对象和局部计算属性混合使用.[即将多个对象合并为一个]
```
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```
 


3.1 Getter ()

 

可认同为store的计算属性,即从store中派生出来一些状态.gett而的返回值会根据他的依赖被缓存起来.

3.1.1 getter接受state 作为第一个参数
```
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```
 


 

 

3.1.2  通过属性访问

getter 会暴露为 store.getters对象,可以通过属性进行访问.

Getter 也可以接受其他 getter 作为第二个参数：

```
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```



 



3.1.3 组件中使用,类似使用state
```
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}﻿​
```

3.1.4 通过方法访问

getter 可以放回一个函数,实现给getter传参,而且通过方法去调用时,每次都会进行调用,不缓存结果.

```
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
//调用
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```
 


 

 

3.1.5 mapGetters  辅助函数

将store 中的getter 函数映射到局部计算属性

```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      doneCount: 'doneTodosCount'
      'anotherGetter',
       // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
    ])
  }
}
```


 

 

4.1mutation（存放改变state的状态的方法）

VUEX 提供commit 方法来修改状态

每个
```
 mutations: {
    increment (state,n) {
      // 变更状态
      state.count++；
    //   state.count +=n;
    }
  }
  // 调用
  store.commit('increment',10)
  
  store.commit({
      type： 'increment',
      params： 10
  })
```

mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)

 

4.1.1 提交 载荷 payload

store.commint传入额外的参数,即mutation的荷载

 

参数可以是单个,也可以是一个对象

4.1.2  对象风格的提交方式
```
store.commit({
  type: 'increment',//将提交的放在一个对象中
  amount: 10
})﻿​
```

4.1.3 mutation 遵守vue的相应规则

1.初始化所需属性

2.对象添加舒心更需要用

vue.set(obj, 'XXX',  123) 或者 新对象替换老对象state.obj = { ... state.obj , XXX: 123}  

 

4.1.4 使用常量替代Mutation事件类型(事件名称)

方法: 用单独的文件 将事件类型替代为 const 常量,暴露出去.

         而后在store.js 中用常量名作为函数名.

4.1.5 mutation 必需是同步函数

因为异步函数的回调是不可追踪的.

4.1.6 组件中提交Mutation

你可以在组件中使用 this.$store.commit('xxx') 提交 mutation，

或者使用 mapMutations 辅助函数将组件中的 methods 映射为 store.commit 调用（需要在根节点注入 store）。

为使调用提交方法与组件的方法一样，可以将他到处重塑。（先import mapMutiations）

```
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}﻿​
```
 

2.5  Action

action  提交的是mutation 不直接更改状态(mutation是同步的，Action是异步的mutation)

可包含任意异步操作

Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象（作为参数！），因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。context 对象为什么不是 store 实例本身了。
```
context {
    commit: ƒ boundCommit(type, payload, options)
    dispatch: ƒ boundDispatch(type, payload)
    getters: {…}
    rootGetters: {…}
    rootState: {__ob__: Observer}
    state: {…}
}﻿​
```

2.5.1 分发Action [触发]

Action 通过 store.dispatch 方法触发,处理 action处理函数返回的promise,并且返回promise

store.dispatch('increment')

Actions 支持同样的载荷方式和对象方式进行分发：

```
// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})﻿
```
 

 

2.5.2调用异步API 和分发多重mutation

```
actions: {
  checkout ({ commit, state }, products) {
    // 把当前购物车的物品备份起来
    const savedCartItems = [...state.cart.added]
    // 发出结账请求，然后乐观地清空购物车
    commit(types.CHECKOUT_REQUEST)
    // 购物 API 接受一个成功回调和一个失败回调
    shop.buyProducts(
      products,
      // 成功操作
      () => commit(types.CHECKOUT_SUCCESS),
      // 失败操作
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}﻿​
```


 

2.5.3组件中分发action

你在组件中使用 this.$store.dispatch('xxx') 分发 action，

或者使用 mapActions 辅助函数将组件的 methods 映射为 store.dispatch 调用（需要先在根节点注入 store）

 
```
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`
      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```

 

 

2.5.4 组合action

利用 async / await，我们可以如下组合 action：

```
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```
 

 

2.6 Module

将store对象切割成模块,每个模块拥有自己的state mutation action getter (嵌套子模块)

```
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态﻿​
```
 

2.6.1 模块的局部状态

对于某块内部的mutation 和getter,接收的第一个参数是模块的局部状态对象.

对于模块内部的 action，局部状态通过 context.state 暴露出来，根节点状态则为 context.rootState

对于模块内部的 getter，根节点状态会作为第三个参数暴露出来：

```
const moduleA = {
  state: { count: 0 },
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },
  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  },
  actions: {
    //   局部状体通过context.state暴露出来
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    },
    getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
  }
}
```
 

 2.6.2 命名空间

 模块内部的action mutation getter 注册在全局命名空间.

模块可设置自己的命名空间,添加namespace: true,会自动根据注册路径调整命名.

 启用了命名空间的 getter 和 action 会收到局部化的 getter，dispatch 和 commit

 2.6.3  在带命名空间的模块内访问全局内容（Global Assets） 

如果你希望使用全局 state 和 getter，rootState 和 rootGetter 会作为第三和第四参数传入 getter，也会通过 context 对象的属性传入 action。

若需要在全局命名空间内分发 action 或提交 mutation，将 { root: true } 作为第三参数传给 dispatch 或 commit 即可。

 

2.6.4  在带命名空间的模块注册全局 action

 在带命名空间的模块注册全局 action，你可添加 root: true，并将这个 action 的定义放在函数 handler 中.

```
modules: {
    foo: {
      namespaced: true,
      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
```
 

2.6.5 带命名空间的绑定函数

当使用 mapState, mapGetters, mapActions 和 mapMutations 这些函数来绑定带命名空间的模块时,可以将模块的空间名称字符串作为第一个参数传递给map.. 函数，这样所有绑定都会自动将该模块作为上下文.

通过使用 createNamespacedHelpers 创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数

 

2.7 插件

1. 使用vuex-along插，件解决刷新问题， store数据没有保留

 ```js
import vuexAlong from 'vuex-along'
import createVuexAlong from 'vuex-along'
new Vuex.Store({
  modules: {
    menu,
    user
  },
  // plugins: [vuexAlong]
  plugins: [
    createVuexAlong({
      name: 'admin', // 设置保存的集合名字，避免同站点下的多项目数据冲突
      local: {
        list: [
          'user.currentMenu',
          'user.userMsg'
        ]
      },
      session: {
        list: [
        ] // 保存 sessionStorage
      }
    })
  ]
})
 ```