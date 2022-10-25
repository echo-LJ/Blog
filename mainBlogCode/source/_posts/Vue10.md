---
title: Vue3.0的优化
date: 2020-11-22 10:00:00
tags: Vue
---

<meta name="referrer" content="no-referrer"/>

#### Vue1.x ----> Vue2.x
引入了`虚拟Dom`的概念： 为后续做`服务端渲染`以及`跨端框架Weex`提供了基础。

#### Vue2.x的痛点 + 其他优化考虑
**痛点**
* 源码自身的维护性
* 数据量大后带来的渲染和更新的性能问题
* 一些想舍弃但为了兼容一直保留的鸡肋API
**其他优化考虑**
* 更好的Typescript支持
* 更好的逻辑复用实践（mixins带来的逻辑模糊）

# Vue3.0的优化

## 一、源码的优化
----
### 1、更好的代码管理方式： monorepo
monorepo： 一个项目仓库（repo）中管理多个模块/包（package）
扩展： 目前有很多大型开源项目采用了这种方式： Babel、 create-react-app、 react-router等。

Vue2.x根据功能拆分出了几个目录：
![image.png](https://s0.lgstatic.com/i/image/M00/2C/DC/Ciqc1F8Cn6mAHaUrAACzRBFsL1g844.png)

* compiler（模板编译的相关代码）
* core（与平台无关的通用运行时代码）
* platforms（平台专有代码）
* server（服务端渲染的相关代码）
* sfc（.vue 单文件解析相关代码）
* shared（共享工具代码） 等目录

Vue3.0根据功能将不同的模块拆分到packages目录下面的不同子目录中：
![image.png](https://s0.lgstatic.com/i/image/M00/2C/DC/Ciqc1F8Cn7KAELkqAAJkxFes1zw593.png)

每个package有各自的API、类型定义、测试。
优点： 使模块拆分更细化、职责划分更明确，模块之间依赖关系更明确。

另外一些package（reactivity响应式库）是可以独立于Vue.js使用的，如果只想使用Vue.js3.0的响应式能力，可以单独依赖这个响应式库而不去依赖整个Vue.js,从而`减小了引用包的体积大小`。

### 2.有类型的Javascript: Typescript

类型语言优点： 有利于代码的维护、避免了类型导致的错误、有利于接口的定义、利于IDE对变量类型的推导。
* Vue1.x没有语法检查
* Vue2.x选择Flow
Flow 是 Facebook 出品的 JavaScript 静态类型检查工具。它可以以非常小的成本对已有的 JavaScript 代码迁入，非常灵活。
但是Flow对于复杂场景类型的检查，支持的不好，在Vue2.x中，有对Flow的吐槽。
```
const propOptions: any = vm.$options.props // wtf flow?
```
由于这里 Flow 并没有正确推导出 vm.$options.props 的类型 ，开发人员不得不强制申明 propsOptions 的类型为 any，显得很不合理.

* Vue3.0使用Typescript 重构了整个项目

## 二、性能的优化
----
### 1、源码体积的优化
JavaScript 包体积越小，意味着网络传输时间越短，JavaScript 引擎解析包的速度也越快。

> Vue.js 3.0 在源码体积的减少方面做了哪些工作呢？

* 首先，移除一些冷门的 `feature`（比如 filter、inline-template 等）；
* 其次，引入 `tree-shaking` 的技术，减少打包体积。

**`tree-shaking`**: 利用ES2015模块语法的静态结构（即 import 和 export），通过`编译阶段`的静态分析，找到没有引入的模块并打上标记。
然后`压缩阶段`会利用例如uglify-js、terser 等压缩工具真正地删除这些没有用到的代码。例如项目中没有用到Transition、keepAlive等组件，对应的代码就不会提交，这样就间接达到了减少Vue.js包体积的目的。
### 2、数据劫持优化
数据劫持：当数据发生改变后，为了自动更新DOM,必须劫持数据的更新，也就是当数据发生改变后能自动执行一些代码去更新DOM，那么Vue.js如何知道更新哪一片DOM呢？（大体的思路）因为在渲染DOM的时候访问了数据，我们对它进行访问劫持，在内部进行依赖关系，也就知道数据对应的DOM是什么了。

* Vue.js1.x和Vue.js2.x是通过`Object.defineProperty`这个Api去劫持数据的getter和setter
**`**缺陷：`**
1.必须知道拦截的Key是什么，他不能检测对象属性的添加和删除
**Vue解决办法:** 提供了`$set`和`$delete`方法。
2. 对于嵌套层级比较深的对象
**Vue解决办法:** 如果要劫持它内部深层次的对象，遍历递归整个对象，执行Object.defineProperty把每一层对象数据都变成响应式的。

* Vue.js3.0s使用了`Proxy API`做数据劫持
1. 他劫持的是整个对象，对于对象属性的增加和删除都能检测到。
2. 对于嵌套层级比较深的对象
**Vue解决办法:** 在Getter中去递归响应式，好处是真正访问到的内部对象才会变成响应式，而不是无脑递归，增加了性能。

### 3、编译优化
![image.png](https://s0.lgstatic.com/i/image/M00/2C/FF/CgqCHl8Cuf2AZw70AAEFU2EMA50521.png)

* Vue.js2.x中从 new Vue 开始渲染DOM的流程，在init阶段处理响应式（数据劫持优化），还有耗时较多的patch阶段。
`diff过程缺点:`
**举个🌰：**一段代码中有多个同级dom节点，但只有一个节点绑定了动态数据。
导致问题：vnode的性能跟模版大小正相关，跟动态节点的数量无关，当一些组件内只有少量动态节点，很多的diff和遍历都是浪费性能，

* Vue3.0 通过编译阶段对静态模版的分析，编译生成了`Block tree`

Block tree是一个将模版基于动态节点指令切割的嵌套区块，每个区块内部的节点结构都是固定的，借助Block tree将vnode更新性能由与模版整体大小提升为与动态内容的数量相关。

除此之外，3，0还包含了对Slot的编译优化，事件侦听函数的缓存优化，并且在运行时重写了diff算法。
## 三、语法API优化： composition API
----
### 1、优化逻辑组织

* Vue.js1.x和Vue.js2.x使用`Options API`
编译组件的时候，一个组件可能有多个逻辑点，当使用ptions API的时候，每一个逻辑关注点都有自己的Options,如果需要修改一个逻辑关注点，就需要在单个文件中不断上下切换和寻找。
* Vue.js3.0使用`Composition API`
某个逻辑关注点相关的代码都放在一个函数里，需要修改一个功能时，就不需要在文件中跳来跳去。

### 1、优化逻辑复用 mixins

**问题：**当一个项目中混入大量mixins，会有两个明显的问题，命名冲突和数据来源不清晰。

mixin可以定义自己的props,data,并且他们之间是无感的，所以很容易定义相同的变量，导致命名冲突，导致数据来源不清晰。

* Vue3.0书写示例：
```
//mousePositionMixin.js
import { ref, onMounted, onUnmounted } from 'vue'
export default function useMousePosition() {
  const x = ref(0)
  const y = ref(0)
  const update = e => {
    x.value = e.pageX
    y.value = e.pageY
  }
  onMounted(() => {
    window.addEventListener('mousemove', update)
  })
  onUnmounted(() => {
    window.removeEventListener('mousemove', update)
  })
  return { x, y }
}
```
使用示例：
```
<template>
  <div>
    Mouse position: x {{ x }} / y {{ y }}
  </div>
</template>
<script>
  import useMousePosition from './mouse'
  export default {
    setup() {
      const { x, y } = useMousePosition()
      return { x, y }
    }
  }
</script>
```
可以看到，整个数据来源清晰了，即时编辑很多的hook函数，也不会出现命名冲突。


## 不兼容
----
Vue3.0是使用ES2015的语法开发的，有些API如Proxy是没有polyfill的，不兼容IE11


----
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [Monorepo——大型前端项目的代码管理方式](https://segmentfault.com/a/1190000019309820?utm_source=tag-newest)
* [https://kaiwu.lagou.com/course/courseInfo.htm?courseId=326#/detail/pc?id=4054](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=326#/detail/pc?id=4054)
