---
title: Vue.js3.0响应式内部的实现原理-原理解析
date: 2020-12-29 14:51:59
tags:
---
<meta name="referrer" content="no-referrer"/>

#### 前情回顾
在 Vue.js 2.x 中，Watcher 就是依赖，有专门针对组件渲染的 render watcher。注意这里有两个流程，首先是依赖收集流程，组件在 render 的时候会访问模板中的数据，触发 getter 把 render watcher 作为依赖收集，并和数据建立联系；然后是派发通知流程，当我对这些数据修改的时候，会触发 setter，通知 render watcher 更新，进而触发了组件的重新渲染。
![image.png](https://s0.lgstatic.com/i/image/M00/36/C0/CgqCHl8YAPSAYotsAAG17TKWHiQ421.png)

`缺点：`Object.defineProperty API 的一些缺点：不能监听对象属性新增和删除；初始化阶段递归执行 Object.defineProperty 带来的性能负担。

## 响应式对象的实现差异
---
1、 在Vue2.x中，只要我们在 data、props、computed 中定义数据，那么它就是响应式的。
`然而在 created 中定义的数据变量并不是响应式对象`是因为Vue内部不会对其做响应式处理（黑盒处理）。

`扩展：`created 中定义的数据变量可以用作组件上下文共享，但是不能用做数据相应。因为创建相应式需要一部分性能代价，可以作为`性能优化小技巧`.

2、在Vue.js3.0中，可以不依赖optionsAPI,而是composition API的setup函数实现相同的功能。
```
<template>
  <div>
    <p>{{ state.msg }}</p>
    <button @click="random">Random msg</button>
  </div>
</template>
<script>
  import { reactive } from 'vue'
  export default {
    setup() {
      const state = reactive({
        msg: 'msg reactive'
      })
      const random = function() {
        state.msg = Math.random()
      }
      return {
        random,
        state
      }
    }
  }
</script>
```
setup中引入了 reactive API，它可以把一个对象数据变成响应式。


可以看出来 Composition API 更推荐用户主动定义响应式对象，而非内部的黑盒处理。这样用户可以更加明确哪些数据是响应式的，如果你不想让数据变成响应式，就定义成它的原始数据类型即可。

## Reactive API
---

reactive 函数
```
function reactive (target) {
   // 如果尝试把一个 readonly proxy 变成响应式，直接返回这个 readonly proxy
  if (target && target.__v_isReadonly) {
     return target
  } 
  return createReactiveObject(target, false, mutableHandlers, mutableCollectionHandlers)
}
function createReactiveObject(target, isReadonly, baseHandlers, collectionHandlers) {
  if (!isObject(target)) {
    // 目标必须是对象或数组类型
    if ((process.env.NODE_ENV !== 'production')) {
      console.warn(`value cannot be made reactive: ${String(target)}`)
    }
    return target
  }
  if (target.__v_raw && !(isReadonly && target.__v_isReactive)) {
    // target 已经是 Proxy 对象，直接返回
    // 有个例外，如果是 readonly 作用于一个响应式对象，则继续
    return target
  }
  if (hasOwn(target, isReadonly ? "__v_readonly" /* readonly */ : "__v_reactive" /* reactive */)) {
    // target 已经有对应的 Proxy 了
    return isReadonly ? target.__v_readonly : target.__v_reactive
  }
  // 只有在白名单里的数据类型才能变成响应式
  if (!canObserve(target)) {
    return target
  }
  // 利用 Proxy 创建响应式
  const observed = new Proxy(target, collectionTypes.has(target.constructor) ? collectionHandlers : baseHandlers)
  // 给原始数据打个标识，说明它已经变成响应式，并且有对应的 Proxy 了
  def(target, isReadonly ? "__v_readonly" /* readonly */ : "__v_reactive" /* reactive */, observed)
  return observed
}
```
reactive 内部通过 createReactiveObject 函数把 target 变成了一个响应式对象。
createReactiveObject 函数主要做了以下几件事情
1. 函数首先判断 target 是不是数组或者对象类型，如果不是则直接返回。所以原始数据 target 必须是对象或者数组。
2. 如果对一个已经是响应式的对象再次执行 reactive，还应该返回这个响应式对象.

reactive 函数会通过 target.__v_raw 属性来判断 target 是否已经是一个响应式对象（因为响应式对象的 __v_raw 属性会指向它自身，后面会提到），如果是的话则直接返回响应式对象。

3. 如果对同一个原始数据多次执行 reactive ，那么会返回相同的响应式对象.

reactive 函数会通过 target.__v_reactive 判断 target 是否已经有对应的响应式对象（因为创建完响应式对象后，会给原始对象打上 __v_reactive 标识，后面会提到），如果有则返回这个响应式对象。

4. 使用 canObserve 函数对 target 对象做一进步限制：
```
const canObserve = (value) => {

  return (!value.__v_skip &&

   isObservableType(toRawType(value)) &&

   !Object.isFrozen(value))

}

const isObservableType = /*#__PURE__*/ makeMap('Object,Array,Map,Set,WeakMap,WeakSet')

```
带有 __v_skip 属性的对象、被冻结的对象，以及不在白名单内的对象如 Date 类型的对象实例是不能变成响应式的。
5. 通过 Proxy API 劫持 target 对象，把它变成响应式。我们把 Proxy 函数返回的结果称作响应式对象，这里 Proxy 对应的处理器对象会根据数据类型的不同而不同.
6. 给原始数据打个标识。
```
target.__v_reactive = observed
```

**响应式的实现方式无非就是劫持数据，Vue.js 3.0 的 reactive API 就是通过 Proxy 劫持数据，而且由于 Proxy 劫持的是整个对象，所以我们可以检测到任何对对象的修改，弥补了 Object.defineProperty API 的不足。**

## Proxy 
---
#### 处理器对象 mutableHandlers 的实现：

```
const mutableHandlers = {
  get,
  set,
  deleteProperty,
  has,
  ownKeys
}
它其实就是劫持了我们对 observed 对象的一些操作，比如：

* 访问对象属性会触发 get 函数；

* 设置对象属性会触发 set 函数；

* 删除对象属性会触发 deleteProperty 函数；

* in 操作符会触发 has 函数；

* 通过 Object.getOwnPropertyNames 访问对象属性名会触发 ownKeys 函数。

因为无论命中哪个处理器函数，它都会做依赖收集和派发通知这两件事其中的一个。

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [https://kaiwu.lagou.com/course/courseInfo.htm?courseId=326#/detail/pc](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=326#/detail/pc)
