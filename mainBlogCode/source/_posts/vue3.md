---
title: 了解vue源码
date: 2017-10-01 10:00:00
tags: Vue
---
<meta name="referrer" content="no-referrer"/>

## new Vue()做了什么？
---
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
---
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
---
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


## 生命周期钩子的合并策略
---
https://www.cnblogs.com/xweizi/p/10572281.html


## 新生命周期钩子：serverPrefetch是什么？
---
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
---
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
---
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
---
* component
* transition
* transition-group
* keep-alive
* slot
## 了解Vue2.6+新全局API：Vue.observable()吗？
---
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
## 了解Vue数据绑定原理？
---
参考Vue.js官网介绍响应式原理图，我们进行分析：

这张图比较清晰地展示了整个流程，首先通过一次渲染操作触发Data的getter（这里保证只有视图中需要被用到的data才会触发getter）进行依赖收集，这时候其实Watcher与data可以看成一种被绑定的状态（实际上是data的闭包中有一个Deps订阅者，在修改的时候会通知所有的Watcher观察者），在data发生变化的时候会触发它的setter，setter通知Watcher，Watcher进行回调通知组件重新渲染的函数，之后根据diff算法来决定是否发生视图的更新。
Vue在初始化组件数据时，在生命周期的beforeCreate与created钩子函数之间实现了对data、props、computed、methods、events以及watch的处理。

Vue在初始化组件数据时，在生命周期的`beforeCreate`与`created`钩子函数之间实现了对`data、props、computed、methods、events`以及`watch`的处理.

* initData
参考源码instance下的state.js文件

initData主要是初始化data中的数据，将数据进行observe，监听数据的变化，其他的监视原理一致，这里以data为例。


```
function initData (vm: Component) {  
  /*得到data数据*/
  let data = vm.$options.data
  data = vm._data = typeof data === 'function' ? getData(data, vm) : data || {} 
  /*判断是否是对象*/
  if (!isPlainObject(data)) {
    data = {}
    process.env.NODE_ENV !== 'production' && warn('data functions should return an object:\n' +'https://vuejs.org/v2/guide/components.html#data-Must-Be-a-Function', vm)
  }  
  // proxy data on instance
  /*遍历data对象*/
  const keys = Object.keys(data)
  const props = vm.$options.props
  let i = keys.length
  //遍历data中的数据
  while (i--) {
    /*保证data中的key不与props中的key重复，props优先，如果有冲突会产生warning*/    if (props && hasOwn(props, keys[i])) {
      process.env.NODE_ENV !== 'production' && warn(`The data property "${keys[i]}" is already declared as a prop. ` +`Use prop default value instead.`,vm)
    } else if (!isReserved(keys[i])) {
      /*判断是否是保留字段*/
      /*这里是我们前面讲过的代理，将data上面的属性代理到了vm实例上*/
      proxy(vm, `_data`, keys[i])
    }
  }
  // observe data
  /*从这里开始我们要observe了，开始对数据进行绑定，这里有尤大大的注释asRootData，这步作为根数据，下面会进行递归observe进行对深层对象的绑定。*/  
  observe(data, true /* asRootData */)
  }
```
其实这段代码主要做了两件事，一是将_data上面的数据代理到vm上，另一件事通过observe将所有数据(data对象中的深层次数据，以及props)变成observable。

* proxy
接下来看一下proxy代理。

```
/*添加代理*/
export function proxy (target: Object, sourceKey: string, key: string) {
  sharedPropertyDefinition.get = function proxyGetter () {
    return this[sourceKey][key]
  }
  sharedPropertyDefinition.set = function proxySetter (val) {
    this[sourceKey][key] = val
  }
  Object.defineProperty(target, key, sharedPropertyDefinition)
}
```

通过proxy函数将data上面的数据代理到vm上，这样就可以用app.text代替app._data.text了。

* observe
参考源码文件在core文件下oberver的index.js文件中


```

/**
 * Attempt to create an observer instance for a value,
 * returns the new observer if successfully observed,
 * or the existing observer if the value already has one.
 */
 /*
 尝试创建一个Observer实例（__ob__），如果成功创建Observer实例则返回新的Observer实例，如果已有Observer实例则返回现有的Observer实例。
 */
export function observe (value: any, asRootData: ?boolean): Observer | void {
  /*判断是否是一个对象*/
  if (!isObject(value)) {
    return
  }
  let ob: Observer | void

  /*这里用__ob__这个属性来判断是否已经有Observer实例，如果没有Observer实例则会新建一个Observer实例并赋值给__ob__这个属性，如果已有Observer实例则直接返回该Observer实例*/
  if (hasOwn(value, '__ob__') && value.__ob__ instanceof Observer) {
    ob = value.__ob__
  } else if (

    /*这里的判断是为了确保value是单纯的对象，而不是函数或者是Regexp等情况。*/
    observerState.shouldConvert &&
    !isServerRendering() &&
    (Array.isArray(value) || isPlainObject(value)) &&
    Object.isExtensible(value) &&
    !value._isVue
  ) {
    ob = new Observer(value)
  }
  if (asRootData && ob) {

    /*如果是根数据则计数，后面Observer中的observe的asRootData非true*/
    ob.vmCount++
  }
  return ob
}

```
Vue的响应式数据都会有一个__ob__的属性作为标记，里面存放了该属性的观察器，也就是Observer的实例，防止重复绑定。
* Observer
Observer的作用就是遍历对象的所有属性将其进行双向绑定。

```
/**
 * Observer class that are attached to each observed
 * object. Once attached, the observer converts target
 * object's property keys into getter/setters that
 * collect dependencies and dispatches updates.
 */
export class  {
  value: any;
  dep: Dep;
  vmCount: number; // number of vms that has this object as root $data

  constructor (value: any) {
    this.value = value
    this.dep = new Dep()
    this.vmCount = 0

    /*
    将Observer实例绑定到data的__ob__属性上面去，之前说过observe的时候会先检测是否已经有__ob__对象存放Observer实例了，def方法定义可以参考https://github.com/vuejs/vue/blob/dev/src/core/util/lang.js#L16
    */
    def(value, '__ob__', this)
    if (Array.isArray(value)) {

      /*
          如果是数组，将修改后可以截获响应的数组方法替换掉该数组的原型中的原生方法，达到监听数组数据变化响应的效果。
          这里如果当前浏览器支持__proto__属性，则直接覆盖当前数组对象原型上的原生数组方法，如果不支持该属性，则直接覆盖数组对象的原型。
      */
      const augment = hasProto
        ? protoAugment  /*直接覆盖原型的方法来修改目标对象*/
        : copyAugment   /*定义（覆盖）目标对象或数组的某一个方法*/
      augment(value, arrayMethods, arrayKeys)

      /*如果是数组则需要遍历数组的每一个成员进行observe*/
      this.observeArray(value)
    } else {

      /*如果是对象则直接walk进行绑定*/
      this.walk(value)
    }
  }

  /**
   * Walk through each property and convert them into
   * getter/setters. This method should only be called when
   * value type is Object.
   */
  walk (obj: Object) {
    const keys = Object.keys(obj)

    /*walk方法会遍历对象的每一个属性进行defineReactive绑定*/
    for (let i = 0; i < keys.length; i++) {
      defineReactive(obj, keys[i], obj[keys[i]])
    }
  }

  /**
   * Observe a list of Array items.
   */
  observeArray (items: Array<any>) {

    /*数组需要便利每一个成员进行observe*/
    for (let i = 0, l = items.length; i < l; i++) {
      observe(items[i])
    }
  }
}
```
Observer为数据加上响应式属性进行双向绑定。如果是对象则进行深度遍历，为每一个子对象都绑定上方法，如果是数组则为每一个成员都绑定上方法。
如果是修改一个数组的成员，该成员是一个对象，那只需要递归对数组的成员进行双向绑定即可。
但这时候出现了一个问题，？如果我们进行pop、push等操作的时候，push进去的对象根本没有进行过双向绑定，更别说pop了，那么我们如何监听数组的这些变化呢？

Vue.js提供的方法是重写 `push、pop、shift、unshift、splice、sort、reverse`这七个数组方法。修改数组原型方法的代码可以参考observer/array.js。
```
/*
 * not type checking this file because flow doesn't play well with
 * dynamically accessing methods on Array prototype
 */

import { def } from '../util/index'

/*取得原生数组的原型*/
const arrayProto = Array.prototype
/*创建一个新的数组对象，修改该对象上的数组的七个方法，防止污染原生数组方法*/
export const arrayMethods = Object.create(arrayProto)

/**
 * Intercept mutating methods and emit events
 */
 /*这里重写了数组的这些方法，在保证不污染原生数组原型的情况下重写数组的这些方法，截获数组的成员发生的变化，执行原生数组操作的同时dep通知关联的所有观察者进行响应式处理*/
;[
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]
.forEach(function (method) {
  // cache original method
  /*将数组的原生方法缓存起来，后面要调用*/
  const original = arrayProto[method]
  def(arrayMethods, method, function mutator () {
    // avoid leaking arguments:
    // http://jsperf.com/closure-with-arguments
    let i = arguments.length
    const args = new Array(i)
    while (i--) {
      args[i] = arguments[i]
    }
    /*调用原生的数组方法*/
    const result = original.apply(this, args)

    /*数组新插入的元素需要重新进行observe才能响应式*/
    const ob = this.__ob__
    let inserted
    switch (method) {
      case 'push':
        inserted = args
        break
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted) ob.observeArray(inserted)
      
    // notify change
    /*dep通知所有注册的观察者进行响应式处理*/
    ob.dep.notify()
    return result
  })
})
```
从数组的原型新建一个Object.create(arrayProto)对象，通过修改此原型可以保证原生数组方法不被污染。如果当前浏览器支持__proto__这个属性的话就可以直接覆盖该属性则使数组对象具有了重写后的数组方法。

如果没有该属性的浏览器，则必须通过遍历def所有需要重写的数组方法，这种方法效率较低，所以优先使用第一种。

在保证不污染不覆盖数组原生方法添加监听，主要做了两个操作，第一是通知所有注册的观察者进行响应式处理，第二是如果是添加成员的操作，需要对新成员进行observe。

但是修改了数组的原生方法以后我们还是没法像原生数组一样直接通过数组的下标或者设置length来修改数组，Vue.js提供了$set()及$remove()方法。

* Watcher

Watcher是一个观察者对象。依赖收集以后Watcher对象会被保存在Deps中，数据变动的时候会由于Deps通知Watcher实例，然后由Watcher实例回调cb进行实图的更新。


```
export default class Watcher {
  vm: Component;
  expression: string;
  cb: Function;
  id: number;
  deep: boolean;
  user: boolean;
  lazy: boolean;
  sync: boolean;
  dirty: boolean;
  active: boolean;
  deps: Array<Dep>;
  newDeps: Array<Dep>;
  depIds: ISet;
  newDepIds: ISet;
  getter: Function;
  value: any;

  constructor (
    vm: Component,
    expOrFn: string | Function,
    cb: Function,
    options?: Object
  ) {
    this.vm = vm
    /*_watchers存放订阅者实例*/
    vm._watchers.push(this)
    // options
    if (options) {
      this.deep = !!options.deep
      this.user = !!options.user
      this.lazy = !!options.lazy
      this.sync = !!options.sync
    } else {
      this.deep = this.user = this.lazy = this.sync = false
    }
    this.cb = cb
    this.id = ++uid // uid for batching
    this.active = true
    this.dirty = this.lazy // for lazy watchers
    this.deps = []
    this.newDeps = []
    this.depIds = new Set()
    this.newDepIds = new Set()
    this.expression = process.env.NODE_ENV !== 'production'
      ? expOrFn.toString()
      : ''
    // parse expression for getter
    /*把表达式expOrFn解析成getter*/
    if (typeof expOrFn === 'function') {
      this.getter = expOrFn
    } else {
      this.getter = parsePath(expOrFn)
      if (!this.getter) {
        this.getter = function () {}
        process.env.NODE_ENV !== 'production' && warn(
          `Failed watching path: "${expOrFn}" ` +
          'Watcher only accepts simple dot-delimited paths. ' +
          'For full control, use a function instead.',
          vm
        )
      }
    }
    this.value = this.lazy
      ? undefined
      : this.get()
  }

  /**
   * Evaluate the getter, and re-collect dependencies.
   */
   /*获得getter的值并且重新进行依赖收集*/
  get () {
    /*将自身watcher观察者实例设置给Dep.target，用以依赖收集。*/
    pushTarget(this)
    let value
    const vm = this.vm

    /*
      执行了getter操作，看似执行了渲染操作，其实是执行了依赖收集。
      在将Dep.target设置为自生观察者实例以后，执行getter操作。
      譬如说现在的的data中可能有a、b、c三个数据，getter渲染需要依赖a跟c，
      那么在执行getter的时候就会触发a跟c两个数据的getter函数，
      在getter函数中即可判断Dep.target是否存在然后完成依赖收集，
      将该观察者对象放入闭包中的Dep的subs中去。
    */
    if (this.user) {
      try {
        value = this.getter.call(vm, vm)
      } catch (e) {
        handleError(e, vm, `getter for watcher "${this.expression}"`)
      }
    } else {
      value = this.getter.call(vm, vm)
    }
    // "touch" every property so they are all tracked as
    // dependencies for deep watching
    /*如果存在deep，则触发每个深层对象的依赖，追踪其变化*/
    if (this.deep) {
      /*递归每一个对象或者数组，触发它们的getter，使得对象或数组的每一个成员都被依赖收集，形成一个“深（deep）”依赖关系*/
      traverse(value)
    }

    /*将观察者实例从target栈中取出并设置给Dep.target*/
    popTarget()
    this.cleanupDeps()
    return value
  }

  /**
   * Add a dependency to this directive.
   */
   /*添加一个依赖关系到Deps集合中*/
  addDep (dep: Dep) {
    const id = dep.id
    if (!this.newDepIds.has(id)) {
      this.newDepIds.add(id)
      this.newDeps.push(dep)
      if (!this.depIds.has(id)) {
        dep.addSub(this)
      }
    }
  }

  /**
   * Clean up for dependency collection.
   */
   /*清理依赖收集*/
  cleanupDeps () {
    /*移除所有观察者对象*/
    let i = this.deps.length
    while (i--) {
      const dep = this.deps[i]
      if (!this.newDepIds.has(dep.id)) {
        dep.removeSub(this)
      }
    }
    let tmp = this.depIds
    this.depIds = this.newDepIds
    this.newDepIds = tmp
    this.newDepIds.clear()
    tmp = this.deps
    this.deps = this.newDeps
    this.newDeps = tmp
    this.newDeps.length = 0
  }

  /**
   * Subscriber interface.
   * Will be called when a dependency changes.
   */
   /*
      调度者接口，当依赖发生改变的时候进行回调。
   */
  update () {
    /* istanbul ignore else */
    if (this.lazy) {
      this.dirty = true
    } else if (this.sync) {
      /*同步则执行run直接渲染视图*/
      this.run()
    } else {
      /*异步推送到观察者队列中，由调度者调用。*/
      queueWatcher(this)
    }
  }

  /**
   * Scheduler job interface.
   * Will be called by the scheduler.
   */
   /*
      调度者工作接口，将被调度者回调。
    */
  run () {
    if (this.active) {
      const value = this.get()
      if (
        value !== this.value ||
        // Deep watchers and watchers on Object/Arrays should fire even
        // when the value is the same, because the value may
        // have mutated.
        /*
            即便值相同，拥有Deep属性的观察者以及在对象／数组上的观察者应该被触发更新，因为它们的值可能发生改变。
        */
        isObject(value) ||
        this.deep
      ) {
        // set new value
        const oldValue = this.value
        /*设置新的值*/
        this.value = value

        /*触发回调渲染视图*/
        if (this.user) {
          try {
            this.cb.call(this.vm, value, oldValue)
          } catch (e) {
            handleError(e, this.vm, `callback for watcher "${this.expression}"`)
          }
        } else {
          this.cb.call(this.vm, value, oldValue)
        }
      }
    }
  }

  /**
   * Evaluate the value of the watcher.
   * This only gets called for lazy watchers.
   */
   /*获取观察者的值*/
  evaluate () {
    this.value = this.get()
    this.dirty = false
  }

  /**
   * Depend on all deps collected by this watcher.
   */
   /*收集该watcher的所有deps依赖*/
  depend () {
    let i = this.deps.length
    while (i--) {
      this.deps[i].depend()
    }
  }

  /**
   * Remove self from all dependencies' subscriber list.
   */
   /*将自身从所有依赖收集订阅列表删除*/
  teardown () {
    if (this.active) {
      // remove self from vm's watcher list
      // this is a somewhat expensive operation so we skip it
      // if the vm is being destroyed.
      /*从vm实例的观察者列表中将自身移除，由于该操作比较耗费资源，所以如果vm实例正在被销毁则跳过该步骤。*/
      if (!this.vm._isBeingDestroyed) {
        remove(this.vm._watchers, this)
      }
      let i = this.deps.length
      while (i--) {
        this.deps[i].removeSub(this)
      }
      this.active = false
    }
  }
}
```
* Dep

来看看Dep类。其实Dep就是一个发布者，可以订阅多个观察者，依赖收集之后Deps中会存在一个或多个Watcher对象，在数据变更的时候通知所有的Watcher。

```

/**
 * A dep is an observable that can have multiple
 * directives subscribing to it.
 */
export default class Dep {
  static target: ?Watcher;
  id: number;
  subs: Array<Watcher>;

  constructor () {
    this.id = uid++
    this.subs = []
  }

  /*添加一个观察者对象*/
  addSub (sub: Watcher) {
    this.subs.push(sub)
  }

  /*移除一个观察者对象*/
  removeSub (sub: Watcher) {
    remove(this.subs, sub)
  }

  /*依赖收集，当存在Dep.target的时候添加观察者对象*/
  depend () {
    if (Dep.target) {
      Dep.target.addDep(this)
    }
  }

  /*通知所有订阅者*/
  notify () {
    // stabilize the subscriber list first
    const subs = this.subs.slice()
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update()
    }
  }
}

// the current target watcher being evaluated.
// this is globally unique because there could be only one
// watcher being evaluated at any time.
Dep.target = null
/*依赖收集完需要将Dep.target设为null，防止后面重复添加依赖。*/
```

* defineReactive

接下来是defineReactive。defineReactive的作用是通过Object.defineProperty为数据定义上getter\setter方法，进行依赖收集后闭包中的Deps会存放Watcher对象。
触发setter改变数据的时候会通知Deps订阅者通知所有的Watcher观察者对象进行试图的更新。

```
/**
 * Define a reactive property on an Object.
 */
export function defineReactive (
  obj: Object,
  key: string,
  val: any,
  customSetter?: Function
) {
  /*在闭包中定义一个dep对象*/
  const dep = new Dep()

  const property = Object.getOwnPropertyDescriptor(obj, key)
  if (property && property.configurable === false) {
    return
  }

  /*如果之前该对象已经预设了getter以及setter函数则将其取出来，新定义的getter/setter中会将其执行，保证不会覆盖之前已经定义的getter/setter。*/
  // cater for pre-defined getter/setters
  const getter = property && property.get
  const setter = property && property.set

  /*对象的子对象递归进行observe并返回子节点的Observer对象*/
  let childOb = observe(val)
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {

      /*如果原本对象拥有getter方法则执行*/
      const value = getter ? getter.call(obj) : val
      if (Dep.target) {

        /*进行依赖收集*/
        dep.depend()
        if (childOb) {

          /*子对象进行依赖收集，其实就是将同一个watcher观察者实例放进了两个depend中，一个是正在本身闭包中的depend，另一个是子元素的depend*/
          childOb.dep.depend()
        }
        if (Array.isArray(value)) {

          /*是数组则需要对每一个成员都进行依赖收集，如果数组的成员还是数组，则递归。*/
          dependArray(value)
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {

      /*通过getter方法获取当前值，与新值进行比较，一致则不需要执行下面的操作*/
      const value = getter ? getter.call(obj) : val
      /* eslint-disable no-self-compare */
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return
      }
      /* eslint-enable no-self-compare */
      if (process.env.NODE_ENV !== 'production' && customSetter) {
        customSetter()
      }
      if (setter) {

        /*如果原本对象拥有setter方法则执行setter*/
        setter.call(obj, newVal)
      } else {
        val = newVal
      }

      /*新的值需要重新进行observe，保证数据响应式*/
      childOb = observe(newVal)

      /*dep对象通知所有的观察者*/
      dep.notify()
    }
  })
}
```
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [「从源码中学习」面试官都不知道的Vue题目答案](https://juejin.im/post/5c959f74f265da610c068fa8)
* [https://github.com/answershuto/learnVue](https://github.com/answershuto/learnVue)




