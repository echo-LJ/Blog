---
title: 组件渲染：vnode 到真实 DOM 是如何转变的-Vue.js3.0？
date: 2020-12-13 10:00:00
tags: Vue
---

<meta name="referrer" content="no-referrer"/>

#### 组件创建好以后是如何被调用并初始化的呢？
因为整个组件树是由`根组件`开始渲染的，我们从应用程序的初始化过程开始分析
在Vue2.x和Vue3.0中初始化一个应用的方式差别不大： 本质上都是把`组件App挂在到id为app的DOM节点上`。
Vue3.0中导入了一个`createApp`:Vue一个入口函数.
createApp函数主要做了两件事情： `创建app对象` + `重写app.mount方法`
## 创建app对象
----
`概念`：利用ensureRenderer().createApp()来创建app对象.

* 其中利用`ensureRenderer()`来延时创建渲染器。
目的： 当用户只依赖响应式包的时候，就不会创建渲染器，通过tree-shaking的方式移除核心渲染逻辑相关的代码。
(ensureRenderer 是在执行 createApp 的时候调用的，如果你不执行 createApp 而只使用 vue 的一些响应式 API，)

`创建app对象-代码思想:` 3.0内部通过createRenderer创建一个渲染器，渲染器内部会有一个createApp方法，他是执行createAppAPI方法返回的函数，函数接受两个参数rootComponent 和 rootProps. 我们在应用层面执行creatApp(App）方法时，会把App组件对象作为根组件传递给rootComponent. 这样，createApp就创建了一个app对象，并会提供mount方法，用来挂载组件。


## 重写app.mount()方法
----
> 创建app对象中提供了mount方法，为什么还要重写？
createApp函数内部的app.mount方法是一个`标准的可跨平台的组件渲染流程:` 创建vnode + 渲染vnode.（web平台有DOM对象）
此方法里面不包含任何与平台相关的逻辑。所以需要重写mount来支持web平台的下的逻辑。

>mount重写做了哪些事情?
* 通过normalizeContainer标准化容器（可以传字符串选择器、或DOM对象， 如果是字符串选择器，先转化成DOM对象，作为最终挂载的容器）。
* 做一个if判断，如果组件对象没有render函数和template模版，则取容器的innerHTML作为组件的模版内容。
* 挂载前清空容器的内容，调用`app.mount方法走标准的组件流程`。

## 创建vnode
----
`vnode本质`：描述DOM的javascript对象。它可以是不同类型的节点，普通元素节点、组件节点等。

**Vue3.0内部针对vnode的type,做了详细的分类，包括Suspense、Teleport等，且把vnode的类型信息做了编码，以便后面的patch阶段，根据不同的类型执行相应的处理逻辑。**

> vnode的优势？
1. 抽象。 引入vnode，可以把渲染过程抽象化，从而使得组件的抽象能力也得到提升。
2. 跨平台。因为patch vnode 的过程不同平台可以有不同的实现，基于vnode做服务端渲染，weex平台、小程序平台的渲染方便很多。

>vnode不意味着不操作DOM,vnode的性能比原声DOM好？
基于vnode实现的MVVM框架，在每次render to vnode的过程中，渲染组件会有一定的js耗时，patch vnode阶段也会耗时。

>Vue.js内部是如何创建vnode的呢？

app.mount()方法中通过`createVNode`函数创建了根组件的vnode:
```
 const vnode = createVNode(rootComponent, rootProps)
```
**createVNode方法**
* 对props做标准化处理： 标准化class、style
* 对vnode的类型信息编码： 方便patch vnode阶段做不同的逻辑处理
* 创建vnode对象： 包含type、 props、 等其他属性
* 标准化子节点children: 把不同数据类型的children转化成文本或数组类型。

## 渲染vnode
----
render渲染函数传入两个参数：vnode, rootContainer:挂载容器
**`render处理逻辑:`**
* if判断vnode,如果为空，执行销毁组件逻辑。
* 不为空，执行`创建组件`或`更新组件`逻辑。
----

### 创建、更新组件-patch()
```
patch = (n1, n2, container, anchor = null, parentComponent = null, parentSuspense = null, isSVG = false, optimized = false) 
```
**`参数分析`**
* n1表示旧的vnode,为null时，表示是一次挂载的过程。
* n2表示新的vnode节点，会根据vnode的类型做不同的逻辑处理。
* container 表示DOM容器，vnode生成DOM后，会挂载到Container下面
**`patch函数逻辑分析`**： 
* 首先， 如果存在新旧节点，且类型不同，则销毁旧节点。
* 其次，根据节点类型不同（组件、普通DOM元素、其他等），做不同渲染逻辑处理。

我们着重看一下对组件+普通DOM元素的处理。
### 对组件的处理-processComponent()
```
 processComponent(n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
```

`函数逻辑` 如果 n1 为 null，则执行挂载组件的逻辑，否则执行更新组件的逻辑。

**挂载组件的 mountComponent 函数的实现**
* 1. 创建组件实例-instance
vue3.x: 通过对象的方式创建了当前渲染的组件实例。
vue2.x: 通过类的方式去实例化组件。
* 2. 设置组件实例-setupComponent(instance)
instance中包含了很多组件相关的数据，维护了组件的上下文，对props、插槽、以及其他实例的属性的初始化处理。
* 3. 设置并运行带副作用的渲染函数-setupRenderEffect(instance）
利用响应式库的 effect 函数创建了一个副作用渲染函数 componentEffect 。
> 副作用: 可以简单地理解为，当组件的数据发生变化时，effect 函数包裹的内部渲染函数 componentEffect 会重新执行一遍，从而达到重新渲染组件的目的。  

effect副作用函数内部会判断是`初始渲染`还是`组件更新`
#### 初始渲染
1. 渲染组件生成subTree-renderComponentRoot()
renderComponentRoot()函数就是去执行 render 函数创建整个组件树内部的 vnode，把这个 vnode 再经过内部一层标准化，就得到了该函数的返回结果：子树 vnode。
2. 把subTree挂载到container中。
调用 patch 函数把子树 vnode 挂载到 container 中.
patch()函数继续对子树 vnode 进行循环类型判断。

>subTree 和 initialVNode的区别？
Vue.js 2.x 中它们分别命名为 _vnode 和 $vnode
在 App 组件中， <hello> 节点渲染生成的 vnode ，对应的就是 Hello 组件的 initialVNode ，为了好记，你也可以把它称作“组件 vnode”。而 Hello 组件内部整个 DOM 节点对应的 vnode 就是执行 renderComponentRoot 渲染生成对应的 subTree，我们可以把它称作“子树 vnode”。


### 对普通 DOM 元素的处理- processElement()
```
 patchElement(n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
```

`函数逻辑` 如果 n1 为 null，则执行挂载元素节点逻辑，否则执行更新元素节点的逻辑。

**挂载元素节点的 mountElement 函数的实现**
* 1. 创建 DOM 元素节点- hostCreateElement()
调用了底层的 DOM API document.createElement 创建元素，所以本质上 Vue.js 强调不去操作 DOM ，只是希望用户不直接碰触 DOM，它并没有什么神奇的魔法，底层还是会操作 DOM。
平台相关的方法是在创建渲染器阶段作为参数传入的
* 2. 处理 props - hostPatchProp()
class、style、event 等属性: 平台相关的属性。
* 3. 处理 children
子节点是纯文本，则执行 hostSetElementText ,它在 Web 环境下通过设置 DOM 元素的 textContent 属性设置文本.
子节点是数组，则执行 mountChildren 方法.
* 4. 挂载 DOM 元素到 container 上。
vue3.x: 通过对象的方式创建了当前渲染的组件实例。
vue2.x: 通过类的方式去实例化组件。
* 2. 设置组件实例-setupComponent(instance)
instance中包含了很多组件相关的数据，维护了组件的上下文，对props、插槽、以及其他实例的属性的初始化处理。
* 3. 设置并运行带副作用的渲染函数-setupRenderEffect(instance）
利用响应式库的 effect 函数创建了一个副作用渲染函数 componentEffect 。
> 副作用: 可以简单地理解为，当组件的数据发生变化时，effect 函数包裹的内部渲染函数 componentEffect 会重新执行一遍，从而达到重新渲染组件的目的。  

effect副作用函数内部会判断是`初始渲染`还是`组件更新`
#### 初始渲染
1. 渲染组件生成subTree-renderComponentRoot()
renderComponentRoot()函数就是去执行 render 函数创建整个组件树内部的 vnode，把这个 vnode 再经过内部一层标准化，就得到了该函数的返回结果：子树 vnode。
2. 把subTree挂载到container中。
调用 patch 函数把子树 vnode 挂载到 container 中.
patch()函数继续对子树 vnode 进行循环类型判断。

>subTree 和 initialVNode的区别？
Vue.js 2.x 中它们分别命名为 _vnode 和 $vnode
在 App 组件中， <hello> 节点渲染生成的 vnode ，对应的就是 Hello 组件的 initialVNode ，为了好记，你也可以把它称作“组件 vnode”。而 Hello 组件内部整个 DOM 节点对应的 vnode 就是执行 renderComponentRoot 渲染生成对应的 subTree，我们可以把它称作“子树 vnode”。








---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接： 
* https://kaiwu.lagou.com/course/courseInfo.htm?courseId=326#/detail/pc
