---
title: Vue.js3.0核心源码解析
date: 2017-02-01 10:00:00
tags: Vue.js3.0
---

<meta name="referrer" content="no-referrer"/>

## Vue.js从出生到进化的演变过程？
* Vue.js 从 1.x 到 2.0 版本，最大的升级就是引入了`虚拟 DOM` 的概念，它为后续做`服务端渲染`以及`跨端框架 Weex` 提供了基础。
* Vue.js 2.x 发展了很久，周边的生态设施都已经非常完善了，但是还有一些存在的问题，例如：

1、源码自身的维护性

2、数据量大后带来的渲染和更新的性能问题
    
3、一些想舍弃但为了兼容一直保留的鸡肋 API 等。

另外，尤小右还希望能给开发人员带来更好的编程体验，比如更好的 `TypeScript 支持`、更好的`逻辑复用实践`等，所以他希望能从`源码`、`性能`和`语法 API` 三个大的方面优化框架。

那么接下来，我们就一起来看一下 Vue.js 3.0 具体做了哪些优化?

---

#  Vue.js 3.0
**`源码优化`**

源码的优化主要体现在使用 monorepo 和 TypeScript 管理和开发源码，这样做的目标是提升自身代码可维护性
## 1、更好的代码管理方式：[monorepo](https://qwqaq.com/2021/08/what-is-monorepo/)：

Vue.js 2.x 的源码托管在 src 目录，然后依据功能拆分出了 `compiler`（模板编译的相关代码）、`core`（与平台无关的通用运行时代码）、`platforms`（平台专有代码）、`server`（服务端渲染的相关代码）、`sfc`（.vue 单文件解析相关代码）、`shared`（共享工具代码） 等目录：

![Vue.js 2.x.png](https://upload-images.jianshu.io/upload_images/11846892-00228d5aea532df9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Vue.js 3.0 ，整个源码是通过 `monorepo` 的方式维护的，根据功能将不同的模块拆分到 `packages` 目录下面不同的子目录中：每个 package 有`各自的 API`、`类型定义`和`测试`。这样使得模块拆分更细化，职责划分更明确，模块之间的依赖关系也更加明确，开发人员也更容易阅读、理解和更改所有模块源码，提高代码的可维护性.

![Vue.js 3.0.png](https://upload-images.jianshu.io/upload_images/11846892-442dbacac20eb503.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

另外一些 package（比如 reactivity 响应式库）是可以独立于 Vue.js 使用的，这样用户如果只想使用 Vue.js 3.0 的响应式能力，可以单独依赖这个响应式库而不用去依赖整个 Vue.js，减小了引用包的体积大小，而 Vue.js 2 .x 是做不到这一点的。

## 2、有类型的 JavaScript：TypeScript

① Vue.js 1.0 没有类型语言，是使用JavaScript 开发了整个框架。

但对于复杂的框架项目开发，使用类型语言非常有利于代码的维护，因为它可以在编码期间帮你做类型检查，避免一些因类型问题导致的错误；也可以利于它去定义接口的类型，利于 IDE 对变量类型的推导.

② Vue.js 2.x 采用了Flow

Flow 是 Facebook 出品的 JavaScript 静态类型检查工具，它可以以非常小的成本对已有的 JavaScript 代码迁入，非常灵活，这也是 Vue.js 2.0 当初选型它时一方面的考量。
    
但是 Flow 对于一些复杂场景类型的检查，支持得并不好。记得在看 Vue.js 2.x 源码的时候，在某行代码的注释中看到了对 Flow 的吐槽，比如在组件更新 props 的地方出现了：

```
const propOptions: any = vm.$options.props // wtf flow?
```

什么意思呢？其实是由于这里 Flow 并没有正确推导出 vm.$options.props 的类型 ，开发人员不得不强制申明 propsOptions 的类型为 any，显得很不合理；另外他也在社区平台吐槽过 Flow 团队的烂尾。


③ Vue.js 3.0 采用 TypeScript 重构了整个项目

TypeScript提供了更好的类型检查，能支持复杂的类型推导；由于源码就使用 TypeScript 编写，也省去了单独维护 d.ts 文件的麻烦；就整个 TypeScript 的生态来看，TypeScript 团队也是越做越好，TypeScript 本身保持着一定频率的迭代和更新，支持的 feature 也越来越多。


**`性能优化`**
## 1、 源码体积优化

JavaScript 包体积越小，意味着网络传输时间越短，JavaScript 引擎解析包的速度也越快。

`Vue.js 3.0 在源码体积的减少方面做了哪些工作呢？`

* 首先，移除一些冷门的 feature（比如 filter、inline-template 等）；

* 其次，引入 tree-shaking 的技术，减少打包体积。

`tree-shaking` 依赖 ES2015 模块语法的静态结构（即 import 和 export），通过编译阶段的静态分析，找到没有引入的模块并打上标记。
然后压缩阶段会利用例如 `uglify-js`、`terser` 等压缩工具真正地删除这些没有用到的代码。

一个 math 模块定义了 2 个方法 square(x) 和 cube(x) ：
```
export function square(x) {

  return x * x

}

export function cube(x) {

  return x * x * x

}

```
在这个模块外面只引入了 cube 方法：
```
import { cube } from './math.js'
// do something with cube
```
最终 math 模块会被 webpack 打包生成如下代码：
可以看到，未被引入的 square 模块被标记了.
```
/* 1 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {
  'use strict';
  /* unused harmony export square */
  /* harmony export (immutable) */ __webpack_exports__['a'] = cube;
  function square(x) {
    return x * x;
  }
  function cube(x) {
    return x * x * x;
  }
});
```

利用 tree-shaking 技术，如果你在项目中没有引入 Transition、KeepAlive 等组件，那么它们对应的代码就不会打包，这样也就间接达到了减少项目引入的 Vue.js 包体积的目的。

## 2、 数据劫持优化

Vue.js 区别于 React 的一大特色是它的数据是响应式的，DOM 是数据的一种映射，数据发生变化后可以自动更新 DOM，用户只需要专注于数据的修改，没有其余的心智负担。

在 Vue.js 内部，想实现数据是响应式的，必须劫持数据的访问和更新。具体的数据的劫持和更新流程图如下：



