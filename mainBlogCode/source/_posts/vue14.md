---
title: Vue.js3.0 & Vue.js2.x 细节对比
date: 2021-01-11 15:26:45
tags:
---

<meta name="referrer" content="no-referrer"/>

## 一个dialog 组件使用的是 position:absolute 绝对定位的方式，如果它的父级 DOM 有 position 不为 static 的布局方式，那么 dialog 的定位就受到了影响，不能按预期渲染了，解决方案？
解决方案是把 dialog 组件渲染的这部分 DOM 挂载到 body 下面。
**Vue.js2.x**
可以依赖开源插件 portal-vue 或者是 vue-create-api实现。

**Vue.js 3.0**
把这一能力内置到内核中，提供了一个内置组件 Teleport。
```
<teleport to="body">
    <Dialog ref="dialog"></Dialog>
  </teleport>
```
to 可以是一个 DOM 选择器字符串，也可以是一个 DOM 节点.

## 通过v-if、v-else频繁切换组件渲染，会触发组件挂载和卸载流程，挂载和卸载都是一个递归过程，会有一定的性能损耗，如何解决呢？
Vue.js 提供了内置组件 KeepAlive
```
<keep-alive>
  <comp-a v-if="flag"></comp-a>
  <comp-b v-else></comp-b>
  <button @click="flag=!flag">toggle</button>
</keep-alive>
```

KeepAlive 是一个抽象组件，它并不会渲染成一个真实的 DOM，只会渲染内部包裹的子节点，并且让内部的子组件在切换的时候，不会走一整套递归卸载和挂载 DOM的流程，从而优化了性能。

## Transition 组件
过渡动画的触发条件:
* 条件渲染 (使用 v-if)；
* 条件展示 (使用 v-show)；
* 动态组件；
* 组件根节点。
在进入/离开过渡的时候会有 6 个 class 切换:
* v-enter-from：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。

* v-enter-active：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。

* v-enter-to：定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter-from 被移除)，在过渡动画完成之后移除。

* v-leave-from：定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。

* v-leave-active：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。

* v-leave-to：定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave-from 被删除)，在过渡动画完成之后移除。