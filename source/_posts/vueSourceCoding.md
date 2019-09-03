---
title: vueSourceCoding
date: 2019-09-03 09:47:57
tags:
---

# 了解vue源码
Author: Echo
Time: 2019-09-03 09:47:57

## new Vue()做了什么？
new关键字代表实例化一个对象, 而Vue实际上是一个类, 源码位置是
`/src/core/instance/index.js`
```
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}
```
接着我们跳转追踪至`this._init()`，即`Vue.prototype._init`,位于`src\core\instance\init.js`.
在`_init()`方法的内部有一系列 `init*` 的方法
```
Vue.prototype._init = function (options?: Object) {
    const vm: Component = this
    // ...忽略，从第45行看起
    if (process.env.NODE_ENV !== 'production') {
      initProxy(vm)
    } else {
      vm._renderProxy = vm
    }
    // expose real self
    vm._self = vm
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm) // resolve injections before data/props
    initState(vm)
    initProvide(vm) // resolve provide after data/props
    callHook(vm, 'created')
    // ...忽略
    if (vm.$options.el) {
      vm.$mount(vm.$options.el)
    }
  }
}
```
统一概述一遍new Vue()时做了什么处理:

* `initProxy`，作用域代理，拦截组件内访问其它组件的数据。
* `initLifecycle`（初始生命周期）, 建立父子组件关系，在当前实例上添加一些属性和生命周期标识。如:$children、$refs、_isMounted等。
* `initEvents`，用来存放除@hook:生命周期钩子名称="绑定的函数"事件的对象。如:$on、$emit等。
* `initRender`，用于初始化$slots、$attrs、$listeners
* `initInjections`，初始化inject，一般用于更深层次的组件通信，相当于加强版的props。用于组件库开发较多。
* `initState`，是很多选项初始化的汇总，包括:props、methods、data、computed 和 watch 等。
* `initProvide`，初始化provide。
* `vm.$mount`，挂载实例。

## 什么阶段才能访问DOM？
这个回答可以从beforeCreate以及 created 的调用时机谈起，我们根据上面的概述，来简化下代码:
```
callHook(vm, 'beforeCreate')
// 初始化 inject
// 初始化 props、methods、data、computed、watch和挂载元素$el(都为undefined)
// 初始化 provide
callHook(vm, 'created')
// 挂载实例 vm.$mount(vm.$options.el)
// vue实例的数据对象data有了
```
所以当面试官问你:

* beforeCreate以及 created 调用时，哪些数据能用与否？
* 什么阶段才能访问DOM？
* 为什么created之后才挂载实例？

## 谈谈你对Vue的生命周期的理解？
生命周期函数解释说明（图片）：https://img2018.cnblogs.com/blog/1475079/201810/1475079-20181015102951341-1694919323.png
##### 理解:
每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做 生命周期钩子 的函数，这给了用户在不同阶段添加自己的代码的机会
##### 生命周期的几个阶段:
1. created/mounted/updated/destroyed，以及对应的before钩子。分别是创建=>挂载=>更新=>销毁
* beforeCreate & created 看上一题
* beforeMount & mounted
```
callHook(vm, 'beforeMount')
// vue实例的$el和data都初始化完成， 挂载之前为虚拟的dom节点，
callHook(vm, 'mounted')
// vue实例挂载完成，data.message成功渲染
```
* beforeUpdate & updated
```
data变化会触发beforeUpdate和updated方法
```
* beforeDestroy & destroyed
```
callHook(vm, 'destroyed')
// data的改变不会再触发周期函数
// vue实例已经解除了事件监听以及和dom的绑定，但是dom结构依然存在。
```

深入理解源码:

2. Vue源码中定义了一个mergeHook函数来遍历一个常量数组LIFECYCLE_HOOKS，该数组实际上是由与生命周期钩子同名的字符串组成的数组。
```
// v2.6.10 最新版
var LIFECYCLE_HOOKS = [
    'beforeCreate',
    'created',
    'beforeMount',
    'mounted',
    'beforeUpdate',
    'updated',
    'beforeDestroy',
    'destroyed',
    'activated',
    'deactivated',
    'errorCaptured',
    // v2.6+ 
    'serverPrefetch' //看下一题
];
```
于是，你可以答多`activated & deactivated`（keep-alive 组件激活/停用）、`errorCaptured`（v2.5 以上版本有的一个钩子，用于处理错误）这三个。

-----

## 生命周期钩子的合并策略
https://www.cnblogs.com/xweizi/p/10572281.html

----- 
单独抽出一天来处理

## 新生命周期钩子：serverPrefetch是什么？
```
// v2.6.10的变化
```
可以看到，`serverPrefetch`前身是`ssrPrefetch`。顾名思义，这是用来处理ssr的。允许我们在渲染过程中“等待”异步数据。可在任何组件中使用，而不仅仅是路由组件。
![image](https://user-gold-cdn.xitu.io/2019/3/25/169b418d400ff0b7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
这里我们贴出一段官方例子：
```
<!-- Item.vue -->
<template>
  <div v-if="item">{{ item.title }}</div>
  <div v-else>...</div>
</template>

<script>
export default {
  computed: {
    item () {
      return this.$store.state.items[this.$route.params.id]
    }
  },
  serverPrefetch () {
    return this.fetchItem()
  },
  mounted () {
    if (!this.item) {
      this.fetchItem()
    }
  },
  methods: {
    fetchItem () {
      // return the Promise from the action
      return this.$store.dispatch('fetchItem', this.$route.params.id)
    }
  }
}
</script>
```


----
整篇文章参考链接：https://juejin.im/post/5c959f74f265da610c068fa8



