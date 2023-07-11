---
title: Vue数据响应式的理解
date: 2023-07-11 13:36:21
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

## 思路分析

1、响应式的概念？
2、为什么Vue需要响应式？
3、响应式的好处？
4、Vue的响应式是如何实现的？有哪些优缺点？
5、Vue3中的响应式的新变化




## 回答范例

1、所谓数据响应式就是**能够使数据变化可以被检测并对这种变化做出响应的机制**
2、MVVM框架中要解决的一个核心问题是：连接数据层和视图层,通过**数据驱动**应用,数据变化 =>视图更新,要做到这点的就需要对数据做响应式处理,这样一旦数据发生变化就立即做出更新操作。（举例简答：解决驱动问题让用户规避细节,例如DOM操作。）
3、以vue为例说明,通过数据响应式加上虚拟DOM和patch算法,开发人员只需要操作数据,关心业务,完全不需要接触繁琐的DOM操作,从而大大提升开发效率,降低开发难度。
4、Vue2中的数据响应式会根据数据的类型做不同的处理，如果是对象则采用 **`Object.defineProperty()`的方式定义数据拦截，当数据被访问或发生变化时，我们感知并做出响应。如果是数组则通过覆盖数组对象原型上的7个变更方法，使这些方法可以做出额外的更新通知，从而做出响应。**这种机制很好的解决了数据响应式变化的问题，但在实际使用时也存在一些**缺点：**比如初始化时的递归会造成性能的浪费，新增或删除属性时需要用户使用`Vue.set/delet`这样的特殊API，对于ES6中新产生的Map、Set这些数据结构不支持的问题。

【缺点：在实现Object.defineProperty() 时，需要循环便利数据对象，在初始化时必须递归，才能实现响应式，如果不使用该数据，则是对性能的浪费。
数组的实现，覆盖了数组的7个方法，才能实现对数组的响应式拦截，实现的复杂，造成了用户使用的复杂度升高，新增和删除属性需要使用`Vue.set/delet`这样的特殊API】
5、Vue3中重新编写这一部分的实现：利用ES6的`Proxy代理`，变成体验好，不需要特殊的api，初始化性能和内存消耗都得到了大幅度改善；另外响应式的实现代码被抽取为独立的`reactivity包`[独立于vue的实现]，使得我们更灵活的使用它，第三方的扩展开发更灵活起来。

【类似于之前的router vuex等不需要依赖，vue只需要使用reactivity包即可】
【初始化遍历的问题，`Proxy代理`可以做懒处理，在数据未访问前不做深层次的递归，直到访问时做响应式处理
`Proxy代理`兼容性差一点】

## vue2的响应式实现：[defineReactive源码地址](https://github1s.com/vuejs/vue/blob/HEAD/src/core/observer/index.ts#L128)
代码梳理：
1、 对object的值做defineProperty处理
2、每次defineReactive只能处理object中某一个key
3、
```
$  let childOb = !shallow && observe(val, false, mock)
```
observe做递归处理，如果当前值仍是对象，所以无论数据是否使用，递归在初始化都会触发。
4、对当前对象的key做拦截，每次get时，做依赖收集。
5、set是通知机制，当数据发生变化时，通知更新，通过`dep.notify()`方法。
## vue3的响应式实现：[reactivity源码地址](https://github1s.com/vuejs/core/blob/HEAD/packages/reactivity/src/reactive.ts)

代码梳理：
1、 createReactiveObject：创建响应式对象，传入对象，与vue2的区别是可直接对对象做处理，不需要知道对象内的key,避免了初始化的递归

2、
```
 const proxy = new Proxy(
    target,
    targetType === TargetType.COLLECTION ? collectionHandlers : baseHandlers
  )
  proxyMap.set(target, proxy)
  return proxy
```
创建新的代理对象，数据被访问时通过collectionHandlers 或baseHandlers处理访问，区分两种Handlers取决于对象的类型
3、mutableHandlers：所有的操作会在该方法中执行

```
export const mutableHandlers: ProxyHandler<object> = {
  get,
  set,
  deleteProperty,
  has,
  ownKeys
}
```
为什么可以取代vue2中数组的特殊实现，和vue.set/delete特殊api,是因为proxy本身支持deleteProperty,set中也支持了动态新增
## 可能追问
1、为什么需要使用`Vue.set/delet`
2、什么是依赖收集，看get方法。

---
总结:大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [查看Vue源码地址](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/renderer.ts#L1549-L1550)

