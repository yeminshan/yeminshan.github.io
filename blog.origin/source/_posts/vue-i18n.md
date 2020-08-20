---
title: vue-i18n
date: 2020-06-08 10:34:17
tags:
categories:
---

# 引入

import Vue from 'vue'
import VueI18n from 'vue-i18n'

Vue.use(VueI18n)

# 使用

const i18n = new VueI18n({
locale: 'CN', // 语言标识
messages : {
en: {
message: {
hello: 'hello world'
}
},
cn: {
message: {
hello: '你好、世界'
}
}
}
})

/_---------使用语言包-----------_/
const i18n = new VueI18n({
locale: 'zh', // 语言标识
//this.\$i18n.locale // 通过切换 locale 的值来实现语言切换
messages: {
'zh': require('./common/lang/zh'), // 中文语言包
'en': require('./common/lang/en') // 英文语言包
}
})

/_ eslint-disable no-new _/
new Vue({
el: '#app',
i18n, //挂载到实例，一定得在这个位置，而不是 comonents 中
template: '<App/>',
components: {
App
}
})

上面的代码正式将 vue-i18n 引入 vue 项目中，创建一个 i18n 实例对象，方便全局调用。我们通过 this.\$i18n.locale 来进行语言的切换。

在组件的 template 中，调用$t()方法
在组件的script中，调用this.$i18n.t()方法
