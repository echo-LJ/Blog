---
title: Vue 的响应式原理
date: 2023-04-21 10:31:53
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>


### 原理概述

Vue.js 的核心在于将`数据模型`和`视图解耦`，通过响应式的机制自动更新视图。

* `数据劫持:` Vue会在组件初始化之前,对数据对象进行遍历劫持，使用`Object.defineProperty`（ES5）或 `Proxy`（ES6）将每个属性转化为`getter/setter`，从而实现对数据对象的监听.

* `依赖收集(追踪):`当组件的数据对象中某个属性被访问时，Vue会收集对该属性的依赖，记录依赖的组件和对应的`watcher`对象,并在属性值被变化时，会触发相应的 setter 操作，然后通知其对应的watcher进行更新，触发对应的回调函数，应用到对应的视图上。

* `派发更新:` 当组件的数据对象的某个属性被修改时，Vue会触发一个更新过程，首先会将新的值赋值给该属性，然后再触发该属性的依赖`Watcher对象`，并通知这些依赖进行更新视图。

---
扩展1：**全局的依赖调度器**
全局的依赖调度器被称为监听者管理器（Watcher Manager），是用来管理组件的 `Watcher 对象`和`数据的 Dep 对象`的全局管理器

扩展2: **更新过程**

1、Vue会对每个Watcher对象进行一次`求值`，以确定该Watcher对象是否收到属性变化的影响,被影响，将Watcher对象放入更新队列中，用于更新视图。

2、如果多个Watcher对象收到属性的变化影响。Vue会对这些Watcher对象进行去重，避免更新相同视图。

3、按照Watcher对象的优先级(非遍历)，更新队列中的Watcher对象，触发Watcher对象的回调函数，将变化同步到相应的视图中（`Vue.js 可以将最小化的视图更新次数，提高应用的执行效率和响应速度`）。

扩展3: **Watcher对象的优先级**

1、`计算属性`的Watcher对象的优先级 > `普通渲染数据`Watcher对象的优先级
2、在创建 Watcher 对象时，Vue.js 会根据其类型和创建方式，将其优先级设置为不同的值。

扩展4: **Watcher对象的求值**

1、Watcher对象有一个`get()`方法，用于求值，决定当前对象是否需要更新视图。
2、在Watcher对象被创建时，会自动执行一次`get()`方法，去初始化Watcher对象的状态，从而完成依赖收集。



## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

