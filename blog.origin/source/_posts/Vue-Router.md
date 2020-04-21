---
title: Vue-Router
date: 2020-03-29 17:44:13
tags: [vue, Vue-Router]
---

路由管理器。
组件映射到路由，告诉路由在哪里渲染这些组件。

## 安装&引入
    // ①下载引入
    <script src="/path/vue-router.js"></script>
    // ②npm 安装引入使用
    npm install vue-router (--save)
    //router/index.js 添加
    import Vue from 'vue'
    import VueRouter from 'vue-router'

    Vue.use(VueRouter)   
    // 全局使用Router，安装

 ## 使用
```html
<!--用于导航的组件-->
<!--to 的值是index文件配置的path-->
<!--  <router-link :to="...">	 === $router.push(...)  -->
<router-link to="/foo">Go to Foo </router-link>﻿​
<router-link to="/Bar">Go to Bar </router-link>

<!--模块将在下面渲染-->
<router-view></router-view>
```

```js
// 1.要先定义组件
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

// 2.定义路由
const routes = [
  { path: '/foo', component: Foo ，
  children：[{  //子路由
      path:'',component:XXX;
  }]},
  { path: 'bar', component: Bar }
]

// 注入路由
const app = new Vue({
  router   //把router里的参数导出
}).$mount('#app')
```

### 路由传参数

1. name
index(routes)配置时添加name属性。

模板中使用 $router.name的形式接受（对象形式）

```
<p> {{$route.name}}</p>
```

2. router-link to属性 传参 （要设置那么属性，对象形式） 这里的name要与路由表对应页面配置的一致

```html
<router-link :to="{name:xxx,params:{key:value}}">valueString</router-link>
```
```js
// 接收
{{$route.params.key}}
```

3. vue-router利用url传递参数
在配置文件已冒号的形式传递参数，导航是不需要冒号

```js
{
    path:'/params/:newsId/:newsTitle',
    component:Params
}
//  正则表达式在url传递的应用
{path:'/params/:newsId(\\d+)/:newsTitle',}
// 接收： 
<p>新闻标题: {{$route.params.newsId}}</p>
<router-link to="/params/198/jspang website is very good">params</router-link>
```

4.路由传参
{ path: 'page/:id', props: true, component: page }
{ path: 'page/:id', props: {'newId': '11'}, component: page }
// 注意 要在路由上设置 props：true,组件可接收params，否则只能从$route里面拿
export default {
  props: ['id']
}
##  命名视图（单页面多路由）
一个页面展示多个 router-view 命名那么属性，不设置默认default
```html
<router-view ></router-view>
 <router-view name="left" style="float:left;width:50%;height:300px;"></router-view>
 <router-view name="right" style="float:right;width:50%;height:300px;"></router-view>
```
```js
{
    path: '/',
    components: {
        default:Hello,
        left:Hi1,
        right:Hi2
    }
}
```

## redirect 重定向
// 转发和重定向
// 转发是服务器行为，服务器在内部完成请求处理和转发动作。 
// 服务器传递的是容器内的request，传输的信息不回丢失
// 重定向是浏览器行,重定向可以访问自己web应用以外的资源，重定向过程中传输的信息会丢失！！ 
// 有数据请求转发，没有重定向
路径不一致,但是打开的是同一个组件
1. 只需将component换成redirect参数
```js
export default new Router({
  routes: [
    {
      path: '/',
      component: Hello
    },{
      path:'/params/:newsId(\\d+)/:newsTitle',
      component:Params
    },{
      path:'/goParams/:newsId(\\d+)/:newsTitle',
      redirect:'/params/:newsId(\\d+)/:newsTitle'
    }]
})
```

2. 起别名
```js
{
    path: '/hi1',
    component: Hi1,
    alias:'/jspang'   // 别名不能为path的字段（重复）
 }
// 跳转直接写别名
<router-link to="/jspang">jspang</router-link>
```


## 路由的过度动画
```
想在路由跳转的时候有过渡动画,在<router-view>标签外添加<transition>标签,并给它一个name属性

这样组件过渡过程中就有四个类名进行切换.他们分别是

[name]-enter                                   进入过渡开始状态

[name]-enter-active                       进入过渡结束状态

[name]-leave                                   离开过渡开始状态

[name]-leave-active                       离开过渡结束状态

过渡模式  mode in-out

过渡模式的mode：
in-out:新元素先进入过渡，完成之后当前元素过渡离开。
out-in:当前元素先进行过渡离开，离开完成后新元素过渡进入。
 
```
路由配置的mode

export default new Router({
    mode:'history'||'hash',//history 如正常的path,hash增加了#/等无意义的字符排列
  routes: [
    {
      path: '/',
      component: Hello
    },{
      path:'/params/:newsId(\\d+)/:newsTitle',
      component:Params
    },{
      path:'/goParams/:newsId(\\d+)/:newsTitle',
      redirect:'/params/:newsId(\\d+)/:newsTitle'
    }]
})
404 页面的设置:
{
   path:'*',
   component:Error //404页面
}
## 嵌套路由
页面内部有导航复用
声明路由时，children里面声明数组即可

注意子路由的占位

## 路由中的钩子
1. 路由配置文件中的钩子函数

只有beforeEnter
{
      path:'/params/:newsId(\\d+)/:newsTitle',
      component:Params,
      beforeEnter:(to,from,next)=>{
        console.log('我进入了params模板');
        console.log(to);// to||form是一个对象,是一个路径信息
        console.log(from);
        next();//路由的控制参数,常用 next(true[跳转]||false[不跳转])||next({path:'/'})
},

2. 模板中的钩子函数

这样可以监控到路由进入和路由的离开
export default {
  name: 'params',
  data () {
    return {
      msg: 'params page'
    }
  },
  beforeRouteEnter:(to,from,next)=>{
    console.log("准备进入路由模板");
    next();
  },
  beforeRouterUpdate(to, from, next) => {
  // 组件实例会被复用时调用
  }
  beforeRouteLeave: (to, from, next) => {
    console.log("准备离开路由模板");
    next();
  }
}

1.导航被触发
2.调用全局的beforeEach守卫
3.在重用的组件里调用beforeRouterUpdate守卫
4.在路由配置里调用beforeEnter
5.在被激活的组件里嗲用beforeRouterEnter
6.调用全局的beforeResolve 守卫
7.导航被确认
8.调用全局的afterEach钩子
9.触发DOM更新




##  编程式的导航
// 编程式的导航
调用 this.$router.push('/xxxx') 
？？ this.$router.repalce
// 注意： 使用path，params不生效
// query 带查询参数 即 => ？...
router.push({name:'user',params:{userId: 123}})
router.push({path: 'register', query: { plan:'private'}})
router.push({ path: `/user/${userId}` }) // -> /user/123


## 路由懒加载
异步组件
什么是懒加载：

原理：先将img标签中的src链接设为同一张图片（空白图片 或者为空），将其真正的图片地址存储再img标签的自定义属性中（比如data-src）。当js监听到该图片元素进入可视窗口时，即将自定义属性中的地址存储到src属性中，达到懒加载的效果。
这样做能防止页面一次性向服务器响应大量请求导致服务器响应慢，页面卡顿或崩溃等问题。

{path: 'home', component: ()=> import('./components/Login')}