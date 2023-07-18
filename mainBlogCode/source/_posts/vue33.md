---
title: 说说对虚拟DOM的理解
date: 2023-07-18 13:41:43
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

## 思路分析

现在框架几乎都引入了虚拟DOM来对真实DOM进行抽象，也就是大家所熟悉的`VNode`和`VDOM`， 那么为什么引入虚拟DOM呢？

1、vdom是什么
2、引入vdom的好处
3、vdom如何生成，又如何成为dom
4、在后续的diff中的作用

极大增加框架的性能+扩展能力

## 回答范例

1、虚拟dom:顾名思义就是虚拟的dom对象，它本身是一个`Javascript`对象，只不过它是通过不同的属性去描述一个视图结构。 【跟真实dom对象相比更轻量，只描述了视图渲染中核心的属性，在计算、遍历渲染中更快速，节省性能】
2、通过引入vdom的好处： 
**将真实的元素节点抽象成VNode,有效减少直接操作dom的次数，从而提高程序性能。**
* 直接操作dom是有限制的，比如：diff, clone等操作，一个真实元素中有很多内容，如果对其进行diff操作，程序会去diff操作一些额外没有必要的内容；同样的，如果进行clone那么就需要对全部内容进行复制，会做一些非必要操作，浪费性能，如果直接操作Javascript对象，就会简单很多。
* 操作dom代价是昂贵的，频繁的dom操作容易引起页面的重绘和回流，通过抽象的VNode进行中间处理，可以有效减少直接操作dom的次数，从而减少页面的重绘和回流。

**方便实现跨平台**

* 同一VNode节点可以渲染成不同平台上的对应内容，比如：渲染在浏览器是dom元素节点，渲染在native(ios， 安卓)变为对应的插件，可以实现ssr、渲染到webGL上等。

3、vdom如何生成？在vue中我们常常为组件编写模版-template,这个模版会被编译器-complier编译为渲染函数，在接下来的挂载（mount）过程中会调用render函数，返回的对象就是虚拟dom,后续通过patch过程进一步转化为真实的dom

![截屏2023-07-18 下午2.10.21.png](https://upload-images.jianshu.io/upload_images/11846892-698bc29cd7522037.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4、挂载过程结束后，vue程序会进入更新流程，如果响应式数据发生变化，会引起组件的重新render,此时会生成新的vdom，和上一次渲染结果进行diff就会得到变化的地方，从而转化成最小量的dom操作，高效更新视图。


* [源码中VNode定义](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/vnode.ts#L127-L128)

**源码中创建vnode** 

* [createElementBlock](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/vnode.ts#L303-L304)
* [createVNode](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/vnode.ts#L506-L507)
可以看到虚拟dom对象的属性值

**[首次调用时刻](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/apiCreateApp.ts#L319-L320)**
第一次产生虚拟dom是在mount挂载函数中，会为根节点创建虚拟dom,在后续会进行转换，变为真实的dom节点

在[render.ts](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/renderer.ts#L1169)中有挂载组件函数`mountComponent`, 参数n2 就是虚拟节点

* **`mountComponent`**内部原理：
* setupComponent： 安装当前的组件实例， 往虚拟dom中将存入：响应式数据进行初始化
* setupRenderEffect： 判断是否已经挂载，如果没有挂载，执行当前组件实例的渲染函数 = 会得到一个虚拟dom树，通过patch进行更新成真实的dom.