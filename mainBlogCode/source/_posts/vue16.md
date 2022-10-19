---
title: Vue.js3.0核心源码解析（2）：vnode 到真实 DOM 是如何转变的？
date: 2022-08-04 10:19:02
tags: Vue.js3.0
---


<meta name="referrer" content="no-referrer"/>


## 概览

![截屏2022-08-05 上午10.59.00.png](https://upload-images.jianshu.io/upload_images/11846892-64395ade5a45268b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


组件系统是 Vue.js 的一个重要概念，它是一种对 DOM 结构的抽象，我们可以使用小型、独立和通常可复用的组件构建大型应用。几乎任意类型的应用界面都可以抽象为一个组件树，如下：
![截屏2022-08-04 上午10.26.49.png](https://upload-images.jianshu.io/upload_images/11846892-1dfb7e998f929e66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

组件化也是 Vue.js 的核心思想之一，它允许我们用模板加对象描述的方式去创建一个组件，再加上我们给组件注入不同的数据，就可以完整地渲染出组件：
```
 模版+对象描述+数据 = 组件
```
当数据更新后，组件可以自动重新渲染，因此用户只需要专注于数据逻辑的处理，而无须关心 DOM 的操作，无论是开发体验和开发效率都得到了很大的提升。



### 应用程序初始化
一个组件可以通过“模板加对象描述”的方式创建，组件创建好以后是如何被调用并初始化的呢？因为整个组件树是由根组件开始渲染的，为了找到根组件的渲染入口，我们需要从应用程序的初始化过程开始分析。

在这里，我分别给出了通过 Vue.js 2.x 和 Vue.js 3.0 来初始化应用的代码：
```
// 在 Vue.js 2.x 中，初始化一个应用的方式如下
import Vue from 'vue'
import App from './App'
const app = new Vue({
  render: h => h(App)
})
app.$mount('#app')
```
```
// 在 Vue.js 3.0 中，初始化一个应用的方式如下
import { createApp } from 'vue'
import App from './app'
const app = createApp(App)
app.mount('#app')
```
Vue.js 3.0 初始化应用的方式和 Vue.js 2.x 差别并不大，本质上都是把 App 组件挂载到 id 为 app 的 DOM 节点上。

但是，在 Vue.js 3.0 中还导入了一个 createApp，其实这是个入口函数，它是 Vue.js 对外暴露的一个函数，我们来看一下它的内部实现：
```
const createApp = ((...args) => {
  // 创建 app 对象
  const app = ensureRenderer().createApp(...args)
  const { mount } = app
  // 重写 mount 方法
  app.mount = (containerOrSelector) => {
    // ...
  }
  return app
})
```

createApp 主要做了两件事情：创建 app 对象和重写 app.mount 方法。

**`1、创建 app 对象`**
首先，我们使用 ensureRenderer().createApp() 来创建 app 对象 ：
```
const app = ensureRenderer().createApp(...args)
```

其中 ensureRenderer() 用来创建一个渲染器对象，它的内部代码是这样的：

```
// 渲染相关的一些配置，比如更新属性的方法，操作 DOM 的方法

const rendererOptions = {

  patchProp,

  ...nodeOps

}

let renderer

// 延时创建渲染器，当用户只依赖响应式包的时候，可以通过 tree-shaking 移除核心渲染逻辑相关的代码

function ensureRenderer() {

  return renderer || (renderer = createRenderer(rendererOptions))

}

function createRenderer(options) {

  return baseCreateRenderer(options)

}

function baseCreateRenderer(options) {

  function render(vnode, container) {

    // 组件渲染的核心逻辑

  }



  return {

    render,

    createApp: createAppAPI(render)

  }

}

function createAppAPI(render) {

  // createApp createApp 方法接受的两个参数：根组件的对象和 prop

  return function createApp(rootComponent, rootProps = null) {

    const app = {

      _component: rootComponent,

      _props: rootProps,

      mount(rootContainer) {

        // 创建根组件的 vnode

        const vnode = createVNode(rootComponent, rootProps)

        // 利用渲染器渲染 vnode

        render(vnode, rootContainer)

        app._container = rootContainer

        return vnode.component.proxy

      }

    }

    return app

  }

}

```

* 首先，用 `ensureRenderer()` 来延时创建渲染器(渲染器可以理解为包含平台渲染核心逻辑的 JavaScript 对象)，好处是当用户只依赖响应式包的时候，就不会创建渲染器，因此可以通过 `tree-shaking` 的方式移除核心渲染逻辑相关的代码。

* 其次，在 Vue.js 3.0 内部通过 createRenderer 创建一个渲染器，这个渲染器内部会有一个 createApp 方法，它是执行 createAppAPI 方法返回的函数，接受了 rootComponent 和 rootProps 两个参数，我们在应用层面执行 createApp(App) 方法时，会把 App 组件对象作为根组件传递给 rootComponent。这样，createApp 内部就创建了一个 app 对象，它会提供 mount 方法，这个方法是用来挂载组件的。

在整个 app 对象创建过程中，Vue.js 利用闭包和函数柯里（缺口缺口缺口）的技巧，很好地实现了参数保留。比如，在执行 app.mount 的时候，并不需要传入渲染器 render，这是因为在执行 createAppAPI 的时候渲染器 render 参数已经被保留下来了。

**`2、重写 app.mount`**

根据前面的分析，我们知道 `createApp` 返回的 app 对象已经拥有了 mount 方法了，但在入口函数中，接下来的逻辑却是对 app.mount 方法的重写。先思考一下，为什么要重写这个方法，而不把相关逻辑放在 app 对象的 mount 方法内部来实现呢？

这是因为 Vue.js 不仅仅是为 Web 平台服务，它的目标是支持`跨平台渲染`，而 createApp 函数内部的 app.mount 方法是一个标准的可跨平台的组件渲染流程：

```
mount(rootContainer) {
  // 创建根组件的 vnode
  const vnode = createVNode(rootComponent, rootProps)
  // 利用渲染器渲染 vnode
  render(vnode, rootContainer)
  app._container = rootContainer
  return vnode.component.proxy
}
```

标准的跨平台渲染流程:
1、先创建 vnode，
2、再渲染 vnode。

此外参数 rootContainer 也可以是不同类型的值：

1、在 Web 平台它是一个 DOM 对象
2、而在其他平台（比如 Weex 和小程序）中可以是其他类型的值

所以这里面的代码不应该包含任何特定平台相关的逻辑，也就是说这些代码的执行逻辑都是与平台无关的。因此我们需要在外部重写这个方法，来完善 Web 平台下的渲染逻辑。

接下来，我们再来看 app.mount 重写都做了哪些事情：

```
app.mount = (containerOrSelector) => {
  // 标准化容器
  const container = normalizeContainer(containerOrSelector)
  if (!container)
    return
  const component = app._component
   // 如组件对象没有定义 render 函数和 template 模板，则取容器的 innerHTML 作为组件模板内容
  if (!isFunction(component) && !component.render && !component.template) {
    component.template = container.innerHTML
  }
  // 挂载前清空容器内容
  container.innerHTML = ''
  // 真正的挂载
  return mount(container)
}
```
* 首先,通过 `normalizeContainer` 标准化容器（这里可以传字符串选择器或者 DOM 对象，但如果是字符串选择器，就需要把它转成 DOM 对象，作为最终挂载的容器）
* 然后, 做一个 if 判断，如果组件对象没有定义 render 函数和 template 模板，则取容器的 innerHTML 作为组件模板内容
* 接着在挂载前清空容器内容，最终再调用 app.mount 的方法走标准的组件渲染流程。

在这里，重写的逻辑都是和 Web 平台相关的，所以要放在外部实现。此外，这么做的目的是既能让用户在使用 API 时可以更加灵活，也兼容了 Vue.js 2.x 的写法，比如 app.mount 的第一个参数就同时支持选择器字符串和 DOM 对象两种类型。

从 app.mount 开始，才算真正进入组件渲染流程，那么接下来，我们就重点看一下核心渲染流程做的两件事情：`创建 vnode` 和`渲染 vnode`。


### 核心渲染流程：创建 vnode 和渲染 vnode

##### 1. 创建 vnode**

`什么是vnode？`用来描述DOM的JavaScript对象，在Vue.js中可以描述不同类型的节点，普通元素节点，组件节点等。
**普通元素节点**
举例如下：
```
<button class="btn" style="width:100px;height:50px">click me</button>
// vnode
const vnode = {
  type: 'button',
  props: { 
    'class': 'btn',
    style: {
      width: '100px',
      height: '50px'
    }
  },
  children: 'click me'
}
```

**组件节点**

距离
```
<custom-component msg="test"></custom-component>
```
