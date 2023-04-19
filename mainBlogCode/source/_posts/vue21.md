---
title: Vue 的生命周期的理解
date: 2023-04-19 10:02:58
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

![vue.png](https://img2018.cnblogs.com/blog/1475079/201810/1475079-20181015102951341-1694919323.png)

1.每个Vue组件实例被创建后都会经过一系列初始化步骤，比如，它需要`数据观测`，`模板编译`，`挂载实例到DOM`上，以及`数据变化时更新DOM`。这个过程中会运行叫做生命周期钩子的函数，以便用户在特定阶段有机会添加他们自己的代码。

2.Vue生命周期总共可以分为8个阶段：**创建前后, 载入前后, 更新前后, 销毁前后**，以及一些特殊场景的生命周期。vue3中新增了三个用于调试和服务端渲染场景。


| 阶段 | vue2 | vue3 | description | function | 可执行 |
| --- | --- | --- | --- | --- | --- |
| 创建阶段| beforeCreate | beforeCreate | 组件实例被创建之初 | 用于插件开发执行初始化任务 | ｜
| 创建阶段| created | created | 组件实例已经被完全创建 | 组件初始化完毕，可以访问各种数据、属性和方法都已完善 | ｜
| 创建阶段| beforeMount | beforeMount | `组件挂载之前`（最后可以修改虚拟DOM的生命周期钩子函数，需谨慎操作，避免对组件渲染性能造成不利的影响） | `组件中的 DOM 操作都在虚拟 DOM 内完成，但尚未将其插入到真正的页面上`。 |在这个钩子函数中，我们可以对虚拟 DOM 进行一些操作，但不可以访问到实例的 `$el `属性。可以通过`vm.$el`获取虚拟DOM实例，我们可以进行一些比较耗时的操作（通过 axios 等工具发送请求，获取需要渲染到组件中的数据，完成一些基础但是必要的初始化工作），但是需要注意对组件渲染性能的影响。｜
| 创建阶段| mounted | mounted | 组件挂载到实例上去之后 | `DOM已创建，可用于获取访问数据和dom元素；访问子组件等` |(保证操作dom的操作开销足够小，在 mounted 阶段完成后，Vue 会采用异步更新机制，如果在此生命钩子中进行的操作过于耗时，则可能会造成渲染性能的问题，甚至会使浏览器出现假死状态。) ｜
| 更新阶段| beforeUpdate | beforeUpdate | `组件数据更新之前`(不会等待异步操作) | `此时Data已经更新，view层还未更新`，可用于获取更新前各种状态（清理旧的监听器、移除无用的信息、更新组件数据）可以访问到`更新前后的数据`,不可以修改组件的数据，否则会进入无限循环（可以使用 `$nextTick` 方法来进行延迟处理） |当`数据状态更新`的时候，Vue 会调用以下的流程：1、触发数据响应式更新 2、重新渲染组件视图 3、触发 beforeUpdate 钩子函数 4、渲染更新到页面 5、触发 updated 钩子函数 ｜
| 更新阶段| updated | updated | 组件数据更新之后 | 完成view层的更新，更新后，所有状态已是最新（可以在此操作更新后的 DOM 界面，例如自动滚动到底部等） | `执行 Vue 组件数据响应式更新和 view 层更新`流程：1、接收更新后的数据，Vue 会将数据转换为虚拟 DOM。2、Vue 会将虚拟 DOM 与上一次渲染的虚拟 DOM 进行比较，找出需要更新的地方。3、根据所找出的需要更新的地方，进行必要的更新操作，包括添加、删除、更新 虚拟DOM 元素等等。4、在更新操作完成之后，Vue 会调用 beforeUpdate 钩子函数。5、Vue 将更新后的 DOM 渲染到页面上，并调用 updated 钩子函数。
|销毁阶段| beforeDestroy | beforeUnmount | 组件实例销毁之前 | 可用于一些定时器或订阅的取消、以避免内存泄漏的问题 |在 钩子函数中，`不能通过访问 this 来访问到组件实例`，因为在组件实例被销毁后，this 将不再指向组件实例。如果需要在这个钩子函数中访问组件实例，可以通过在函数内部保存一个指向组件实例的引用来实现，并且不能阻止组件实例被销毁，否则会`内存泄漏`| 
|销毁阶段| destroyed | **unmounted** | 组件实例销毁之后 | 可清理它与其它实例的连接，解绑它的全部指令及事件监听器|路由切换时，如果从一个组件切换到另一个组件，那么当前组件的 `destroyed 钩子函数`可能不会被调用(使用 $route 对象来监视路由的变化，并在路由变化时手动清理组件)| 
| | acivated | onActivated | keep-alive缓存的组件激活时| | | 
| | deactivated | onDeactivated | keep-alive缓存的组件停用时| | | 
| | errorCaptured | errorCaptured | 捕获来自子孙组件的错误时|钩子函数接收的两个参数分别是错误和组件实例|钩子函数并不会捕获 JavaScript 自身的错误，例如语法错误或者浏览器本身引起的错误。它只能捕获由组件内部抛出的错误，例如使用 throw 关键字抛出的错误、Promise.reject 返回的错误等等。如果需要捕获 JavaScript 自身的错误，可以使用 window.onerror 全局错误处理函数。| 
| | - | **renderTracked** | 调试钩子，响应式依赖被收集时| | | 
| | - | **renderTriggered** | 调试钩子，响应式依赖被触发时| | | 
| | - | **serverPrefetch** | ssr only，组件实例在服务器上被渲染前调用| | | | 



### 父子组件生命周期执行顺序
* Vue 3 中父子组件的生命周期函数的执行顺序如下：
1、父组件 beforeCreate
2、父组件 created
3、父组件 beforeMount
4、子组件 beforeCreate
5、子组件 created
6、子组件 beforeMount
7、子组件 mounted
8、父组件 mounted

* 当组件更新阶段时，组件的生命周期函数执行顺序如下：
1、父组件 beforeUpdate
2、子组件 beforeUpdate
3、子组件 updated
4、父组件 updated

* 当组件被销毁时，组件的生命周期函数执行顺序如下：
1、子组件 beforeUnmount
2、子组件 unmounted
3、父组件 beforeUnmount
4、父组件 unmounted

### 使用 keep-alive 缓存组件，父子组件生命周期执行顺序
1、父组件 beforeCreate
2、父组件 created
3、父组件 beforeMount
4、子组件 beforeCreate
5、子组件 created
6、子组件 beforeMount
7、子组件 mounted
8、父组件 mounted
9、父组件 deactivated
10、子组件 deactivated
11、子组件 activated
12、父组件 activated

### 路由切换时组件的 destroyed 钩子函数不会被调用?
可能不会被调用,原因如下：
* 1、异步操作导致的组件销毁延时

在组件销毁的过程中，如果组件实例上存在一些`异步操作`（例如定时器、Promise 等等），这些异步操作可能会导致组件的销毁延时。如果在组件销毁之前，异步操作还未完成，那么组件的销毁流程将被打断，从而导致 `destroyed` 钩子函数不被调用。

* 2、懒加载组件的情况

在使用懒加载（也称为按需加载）方式加载组件时，组件实例并不会在组件被切换后立即被销毁，而是会等待一段时间，直到没有其它组件依赖于它才被销毁。在这种情况下，组件的 `destroyed` 钩子函数也可能不会被调用。

为了避免由于未执行 `destroyed` 钩子函数而导致的内存泄漏等问题，可以在组件中手动监听路由的变化，并在路由变化时手动清理组件，在 `beforeUnmount `钩子函数中执行清理工作以确保在组件卸载之前清理组件。


### 新生命周期钩子：serverPrefetch是什么？


`serverPrefetch` 是 Vue 3 中新增的一种异步数据预取技术，用于在组件渲染之前，提前获取组件所需的异步数据，以便在渲染时直接使用，从而提升页面加载速度和用户体验。

在 Vue 3 中，通过在组件内部定义 serverPrefetch 静态方法，可以在`服务器端渲染时`、`客户端路由导航时`或`预渲染时调用`该方法，预取组件所需的异步数据。在执行 serverPrefetch 方法时，可以返回一个 Promise 对象，该 Promise 对象用于异步获取组件所需的数据，并在完成后将其返回。在组件渲染时，可以通过 `ssrContext` 对象访问到预取的数据，然后渲染出组件所需要的 HTML 代码。

使用 serverPrefetch 技术，可以充分利用服务器端的异步数据获取能力，提前获取组件所需的数据，从而降低客户端渲染的延迟和带宽占用，提高用户体验和搜索引擎优化效果。

需要注意的是，serverPrefetch 技术需要与服务器端渲染（SSR）、客户端路由导航等技术密切配合，才能实现最佳效果。同时，在使用 serverPrefetch 时需要注意数据的格式、缓存策略等问题，以便避免数据重复请求、数据不一致等问题。


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

### ssrContext的使用方法

在 Vue 3 中，`ssrContext `变量是在服务器端渲染（SSR）时使用的`上下文对象`，在组件渲染过程中用于传递一些额外的数据或方法。

`ssrContext` 变量是在渲染过程中被注入到组件上下文中的一个特殊对象，它包括了一些与服务器渲染相关的属性和方法。在组件中，可以通过 `this.$ssrContext` 访问到当前上下文的 ssrContext 对象。

在服务器渲染过程中，可以在创建 Vue 应用程序实例时，将其作为参数传递给 `createApp` 方法，例如：

```
const app = createApp({
  // 应用程序组件
  /* ... */
}).provide('ssrContext', ssrContext);

app.mount('#app');
```
在组件的 `serverPrefetch` 钩子函数中，可以通过 `this.$ssrContext` 访问到 `ssrContext` 对象，从而获取服务器端渲染过程中预取的数据。例如：

```
export default {
  serverPrefetch() {
    const { ssrContext } = this.$ssrContext;
    return fetch('/api/data').then((res) => {
      ssrContext.data = res.data;
    });
  },
};
```

在上面的例子中，`serverPrefetch` 函数通过 fetch 方法异步获取了 `/api/data` 接口的数据，并将其存储到 `ssrContext` 对象中的 data 属性中。在组件渲染过程中，可以通过访问 `ssrContext 对象`来获取预取的数据，例如：

```

<template>
  <div>
    <p>{{ $ssrContext.data }}</p>
  </div>
</template>
```

在上面的例子中，`$ssrContext` 对象的 data 属性就是在 `serverPrefetch` 中存储到 `ssrContext 对象`中的数据。

综上所述，ssrContext 变量是 Vue 3 中在服务器端渲染过程中使用的上下文对象，在组件渲染过程中用于传递一些额外的数据或方法。通过在创建 Vue 应用程序实例时传递给 createApp 方法，可以将其注入到组件上下文中，从而在组件中访问 ssrContext 对象，实现服务器端渲染过程中数据的传递和获取。


## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️