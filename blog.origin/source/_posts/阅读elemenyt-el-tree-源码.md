---
title: 阅读elemenyt el-tree 源码
date: 2020-04-21 16:07:29
tags:
categories:
---


### [el-tree 入口](https://github.com/ElemeFE/element/blob/dev/packages/tree/index.js)

```js
import Tree from './src/tree.vue';
/* 这里使用的是全局注册组件的方法开发的插件 */
Tree.install = function(Vue) {
  Vue.component(Tree.name, Tree);
};
export default Tree;
```

### [tree.vue](https://github.com/ElemeFE/element/blob/dev/packages/tree/src/tree.vue#L41)

```js
 import TreeStore from './model/tree-store'; 
  import { getNodeKey, findNearestComponent } from './model/util';
  import ElTreeNode from './tree-node.vue'; // components: { ElTreeNode },
  import {t} from 'element-ui/src/locale';
  import emitter from 'element-ui/src/mixins/emitter'; // mixins: [emitter],
  import { addClass, removeClass } from 'element-ui/src/utils/dom';
```