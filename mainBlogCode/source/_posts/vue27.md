---
title: 如何扩展一个Vue组件
date: 2023-07-06 13:42:17
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>


**组件扩展: **对支持一个功能的组件进行扩展，让其支持更多功能.

## 思路分析
1、按照逻辑扩展和内容扩展来列举
    * 逻辑扩展: mixins、extends、composition api;
    * 内容扩展: slots
2、分别说出他们的使用方法、场景差异和问题。
3、作为扩展,还可以说说Vue3中新引入的`composition Api`带来的变化。

## 回答范例
1、常见的组件扩展方法有: mixins、extends、slots等;
2、混入`mixins`在分发Vue组件的可复用的功能很灵活【混入对象可以包含任意组件选项。当组件使用`mixins`, 所有`mixins`对象的选项将被混入组件本身的对象中】。但是也存在一些问题。

例如：过去在项目开发中使用`mixins`,有全局的`mixins`和局部的`mixins`两种方式，当`mixins`比较多的时候,就可能会出现以下几个问题。
* `mixinA` 和 `mixinB`可能会冲突: 变量名或方法名冲突
* `mixins` 和 组件本身可能会冲突: 变量名或方法名冲突

所以在使用过程中会**`造成来源不明`**。

3、插槽主要用于vue组件中的内容分发, 也可以用于组件扩展。

* 使用场景: 有一个容器型组件A,内容不确定,需要通过分发和扩展的方式传进来。

```
// 子组件Child

<div>
    <slot>这个内容会被父组件传递的内容替换</slot>
</div>

// 父组件Parent
<div>
    <Child>这个内容会被父组件传递的内容替换</Child>
</div>
```
如果要精确分发到不同位置可以使用具名插槽,如果要使用子组件中的数据可以使用作用域插槽。


源码: 组件在初始化时要先将slots编译的结果存到固定位置,子组件通过访问父组件取到slots编译后的内容。
* 子组件使用slots的内容
* 父组件提供slots的内容

4、扩展组件中也可以使用`extends`, 可以起到扩展组件的目的。

```
// 扩展对象

const myextends = {
    methods: {
        dosomething(){}
    }
}

// 组件扩展: 做数组项设置到`extends`选项,仅作用于当前组件。
// 跟`mixins`的区别: `extends`只能扩展单个对象。
// 如果和`mixins`发生冲突,`extends`的优先级更高。

const comp = {
    extends: myextends
}
```

5、`mixins`的数据和方法不能明确判断来源, Vue3中引入 `Composition Api`可以很好的解决这些问题。利用独立出来的响应式模块可以很方便的编写独立逻辑并提供响应式数据,然后再`setup`选项中组合使用,增强代码的`可读性`和`可维护性`。
```
// 复用逻辑1
function useXX(){}

// 复用逻辑2
function useYY(){}

// 逻辑组合
const Comp = {
    setup(){
        const {xx} = useXX()
        const {yy} = useYY()
        return {xx, yy}
    }
}
```

### `mixins`使用方法
* vue2 中

```
// mixins复用代码
const mymixin = {
    methods: {
        dosomething(){}
    }
}
// 全局混入: 将mixins 混入对象传入

Vue.mixin(mymixin)

// 局部混入: 做数组项设置到mixins选项,仅作用于当前组件。

const comp = {
    mixins: [mymixin]
}
```

* vue3中如何使用？

* Vue3源码中mixins主要做了两件事
1、将开发者的mixins添加到上下文数组中:
2、通过applyOptions方法中的resolveMergeOptions将混入对象和组件中的选项进行合并。
3、merge策略: 局部的mixins优先级 > 全局 和组件中选项。
### Vue.extend方法你用过么？它能用来做组件扩展么？


* vue2中使用的是options Api(mixins、extends);
* extends: 在options api中有一个extends api;实现组件继承功能; 例如: A组件继承B组件的数据和方法, 从而扩展出新的数据和方法。A和B组件会融合在一起。

* mixins、extends、composition api之间的区别？
* 为什么使用slots ？ 有哪几种？ vue2和3的区别
* 作用域插槽 和 具名插槽是什么？

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接：

* [查看Vue源码地址](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/renderer.ts#L1549-L1550)