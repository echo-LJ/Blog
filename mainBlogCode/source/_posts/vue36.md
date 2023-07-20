---
title: 如何从0手写Vue-router？说说你的思路？
date: 2023-07-20 13:52:00
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

## 思路分析
首先思考vue-route要解决的问题：用户点击链接跳转切换内容，页面不刷新。

1、借助hash和history api实现url跳转页面不刷新
2、同时监听hashchange事件或者popstate事件处理跳转
3、根据hash值或state值从routes表中匹配对应的component并渲染

## 回答范例
一个SPA应用的路由需要解决的问题是`页面跳转内容改变同时页面不刷新`，同时路由需要以插件的形式存在，所以：
1、首先会定义一个`creatRouter`函数，返回路由器实例，实例内部做几件事：
* 保存用户传入的配置项
* 监听hash或者popstate事件： 如果监听到变化，设置一个响应式数组【当前url的地址】，根据用户传入的配置，可以得到url的path和组件的映射。
* 回调里根据path匹配对应路由: 然后在`router-view`中render这个组件。

2、将route定义成一个Vue插件，即实现install方法，内部做两件事：
* 实现两个全局组件：`router-link`和 `router-view`，分别实现页面跳转和页面显示
* 定义两个全局变量：`$route` 和`$router`组件可以访问当前路由和路由器实例。


## 源码分析

* [creatRouter如何创建实例](https://github1s.com/vuejs/router/blob/HEAD/packages/router/src/router.ts#L355-L356)

creatRouter 是一个工厂函数, return一个路由器实例，常见的addRoute、等方法已经注册过了
```
const router: Router = {
    currentRoute,
    listening: true,

    addRoute,
    removeRoute,
    hasRoute,
    getRoutes,
    resolve,
    options,

    push,
    replace,
    go,
    back: () => go(-1),
    forward: () => go(1),

    beforeEach: beforeGuards.add,
    beforeResolve: beforeResolveGuards.add,
    afterEach: afterGuards.add,

    onError: errorListeners.add,
    isReady,

    install(app: App) {
      const router = this
      app.component('RouterLink', RouterLink)
      app.component('RouterView', RouterView)
      app.config.globalProperties.$router = router
```
install 方法可以得到vue的应用程序实例，注册两个router全局组件， 接下来注册全局的路由器实例，用户在组件中可以通过`this.$router`来访问到当前路由器的实例。

* [事件监听](https://github1s.com/vuejs/router/blob/HEAD/packages/router/src/history/html5.ts)

可以看到事件监听
```
window.removeEventListener('popstate', popStateHandler)
window.removeEventListener('beforeunload', beforeUnloadListener)
```
* [router-link](https://github1s.com/vuejs/router/blob/HEAD/packages/router/src/RouterLink.ts)
* [router-view](https://github1s.com/vuejs/router/blob/HEAD/packages/router/src/RouterView.ts)

---
总结:大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

