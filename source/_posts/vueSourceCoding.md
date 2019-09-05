---
title: 了解vue源码
date: 2019-09-03 09:47:57
tags: vue
---

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

* `initProxy`，作用域代理，拦截组件内访问其它组件的数据。(支持Proxy且处于开发环境下, 对未声明就使用的属性进行提示)
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
## Vue-router 路由模式有几种？
三种 "hash" | "history" | "abstract"，一般人只知道两种"hash" | "history"
```
// 源码：

switch (mode) {
  case 'history':
    this.history = new HTML5History(this, options.base)
    break
  case 'hash':
    this.history = new HashHistory(this, options.base, this.fallback)
    break
  case 'abstract':
    this.history = new AbstractHistory(this, options.base)
    break
  default:
    if (process.env.NODE_ENV !== 'production') {
      assert(false, `invalid mode: ${mode}`)
    }
}
```
##### mode
* 类型: string
* 默认值: "hash" (浏览器环境) | "abstract" (Node.js 环境)
* 可选值: "hash" | "history" | "abstract"

配置路由模式:

* hash: 使用 URL hash 值来作路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器。
* history: 依赖 HTML5 History API 和服务器配置。查看 HTML5 History 模式。
* abstract: 支持所有 JavaScript 运行环境，如 Node.js 服务器端。如果发现没有浏览器的 API，路由会自动强制进入这个模式.

###### 路由模式解析
对于Vue 这类渐进式前端开发框架，为了构建SPA（单页面应用），需要引入前端路由系统，这也就是Vue-router存在的意义.

前端路由的核心，就在于——— 改变视图的同时不会向后端发出请求
`
浏览器对页面的访问是无状态的，所以我们在切换不同的页面时都会重新进行请求。
实际运用vue和vue-router开发就会发现，在切换页面时是没有重新请求的，使用起来就好像页面是有状态的。
其实是借助浏览器的History API来实现的，可以使页面跳转而不刷新，页面的状态就维持在浏览器中了。
`

.

* vue-router: hash

hash模式中url带有#号，修改成history模式，url中的#自动就去除了。

hash模式背后的原理是onhashchange事件，可以在window对象上监听这个事件：
```
window.onhashchange = function(event){
    console.log(event.oldURL, event.newURL);
    let hash = location.hash.slice(1)
}
```

hash模式下，仅hash符号之前的内容会被包含在请求中,如 http://www.abc.com, 因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回404错误,因此改变hash不会重新加载页面.

* vue-router: history
随着history api的到来，前端路由开始进化了，前面的hashchange，你只能改变#后面的url片段，而history api则给了前端完全的自由。
　history api可以分为两大部分：切换和修改
1. 切换历史状态

包括back、forward、go三个方法，对应浏览器的前进，后退，跳转操作，有同学说了，(谷歌)浏览器只有前进和后退，没有跳转，嗯，在前进后退上长按鼠标，会出来所有当前窗口的历史记录，从而可以跳转：
```
history.go(-2);//后退两次
history.go(2);//前进两次
history.back(); //后退
hsitory.forward(); //前进
```
`
坑1：此处有一个开发的坑：在我们项目中，开发人员会在某个详情页面按钮上绑定history.go(-1)用来进入详情的主页面，
有时测试人员会直接输入url进入详情页，这样点击按钮就会出现问题，跳转的就不是项目中的主页面，就会是浏览器历史记录中的上一页
`

2. 修改历史状态
包括了pushState、replaceState两个方法，这两个方法接收三个参数：stateObj，title，url
```
history.pushState(stateObj，title，url)
window.onpopstate = function(event){
    console.log(event.state)
    if(event.state && event.state.color === 'red'){
        document.body.style.color = 'red';
    }
}

```
`
坑2：通过history api，我们丢掉了丑陋的#，但是它也有个毛病：不怕前进，不怕后退，就怕刷新，f5，（如果后端没有准备的话），因为刷新是实实在在地去请求服务器的。在hash模式下，前端路由修改的是#中的信息，而浏览器请求时是不带它玩的，所以没有问题。但是在history下，你可以自由的修改path，当刷新时，如果服务器中没有相应的响应或者资源，会分分钟刷出一个404来。
`
3. popstate实现history路由拦截，监听页面返回事件
当活动历史记录条目更改时，将触发popstate事件

*  如果被激活的历史记录条目是通过对  history.pushState()  的调用创建的，或者受到对  history.replaceState()  的调用的影响，popstate事件的state属性包含历史条目的状态对象的副本。

* 需要注意的是调用  history.pushState()  或  history.replaceState()  用来在浏览历史中添加或修改记录，不会触发popstate事件；

  　　只有在做出浏览器动作时，才会触发该事件，如用户点击浏览器的回退按钮（或者在Javascript代码中调用history.back()）
* vue-router: abstract
abstract模式是使用一个不依赖于浏览器的浏览历史虚拟管理后端。

根据平台差异可以看出，在 Weex 环境中只支持使用 abstract 模式。 不过，vue-router 自身会对环境做校验，如果发现没有浏览器的 API，vue-router 会自动强制进入 abstract 模式，所以 在使用 vue-router 时只要不写 mode 配置即可，默认会在浏览器环境中使用 hash 模式，在移动端原生环境中使用 abstract 模式

## 谈谈你对keep-alive的了解
先贴一个常规回答：
`
keep-alive是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染。
在vue 2.1.0 版本之后，keep-alive新加入了两个属性: include(包含的组件缓存) 与 exclude(排除的组件不缓存，优先级大于include) 。
<keep-alive> 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。
`

`然后你可以开始骚了：`

1. `<keep-alive>`是 Vue 源码中实现的一个全局抽象组件，通过自定义 `render` 函数并且利用了`插槽`来实现数据缓存和更新。它的定义在`src/core/components/keep-alive.js` 中：
```
export default {
  name: 'keep-alive',
  abstract: true,
  ...
}
```
2. 所有的抽象组件是通过定义abstract选项来声明的。抽象组件不渲染真实DOM，且不会出现在父子关系的路径上（initLifecycle会忽略抽象组件），相关代码片段：
```
if (parent && !options.abstract) {
  // abstract 即 `ptions.abstract`
  // while 循环查找第一个非抽象的父组件
  while (parent.$options.abstract && parent.$parent) {
    parent = parent.$parent
  }
  parent.$children.push(vm)
}
```

3.在 2.2.0 及其更高版本中，activated 和 deactivated 将会在 <keep-alive> 树内的所有嵌套组件中触发。

## vue源码中内置的组件？
* component
* transition
* transition-group
* keep-alive
* slot
## 了解Vue2.6+新全局API：Vue.observable()吗？
Vue2.6+新的全局API是Vue.observable(),它的使用方式：
```
// 准备个文件store.js - /store/store.js
import Vue from 'vue'

export const store = Vue.observable({ count: 0 })  //定义一个变量
export const mutations = {  //定义一个方法，将来在组件中调用这个方法从而能改变上面的变量count值
  setCount (count) {
    store.count = count
  }
}
```
返回的对象可以直接用于`渲染函数`和`计算属性`内，并且会在发生改变时触发相应的更新。
```
<template>
    <div>
        <p>你点+-，看我能不能根据状态去动态改变</p>
        <label for="bookNum">数量</label>
        <button @click="setCount(count+1)">+</button>
        <span>{{count}}</span>
        <button @click="setCount(count-1)">-</button>
    </div>
</template>

<script>
import { store, mutations } from '../store/store' // Vue2.6新增API Observable

export default {
  name: 'Add',
  computed: {
    count () {
      return store.count //用于去渲染之前Observable中定义的变量count
    }
  },
  methods: {
    setCount: mutations.setCount
  }
}
</script>
```
----
整篇文章主要参考链接：https://juejin.im/post/5c959f74f265da610c068fa8



