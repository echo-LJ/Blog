---
title: nextTick的使用和原理
date: 2023-05-06 10:09:27
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

### nextTick是做什么的?
[nextTick](https://link.juejin.cn?target=https%3A%2F%2Fstaging-cn.vuejs.org%2Fapi%2Fgeneral.html%23nexttick "https://staging-cn.vuejs.org/api/general.html#nexttick")是等待下一次 DOM 更新刷新的工具方法。

### 为什么需要nextTick呢?

Vue有个异步更新策略，在 Vue 中更改响应式状态时，最终的 DOM 更新并不是同步生效的，而是由 Vue 将它们缓存在一个队列中，同一事件循环中发生的所有数据变更会异步的批量更新。这样是为了确保每个组件无论发生多少状态改变，都仅执行一次更新。如果想立即获取DOM更新后的状态，就需要使用nextTick。


### 开发时使用nextTick的时机?

-   created中想要获取DOM时；
-   响应式数据变化后获取DOM更新后的状态，比如希望获取列表更新后的高度。

### 如何使用nextTick？

nextTick() 可以在状态改变后立即使用，以等待 DOM 更新完成。你可以传递一个回调函数作为参数，或者 await 返回的 Promise.

```
// nextTick签名如下：
function nextTick(callback?: () => void): Promise<void>
```

```
// 使用示例：

<script>
import { nextTick } from 'vue'

export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    async increment() {
      this.count++

      // DOM 还未更新
      console.log(document.getElementById('counter').textContent) // 0

      await nextTick()
      // DOM 此时已经更新
      console.log(document.getElementById('counter').textContent) // 1
    }
  }
}
</script>

<template>
  <button id="counter" @click="increment">{{ count }}</button>
</template>
```

### 为什么在nextTick回调函数中可以访问到DOM更新后的状态呢？
在Vue内部，nextTick之所以能够让我们看到DOM更新后的结果，是因为我们传入的callback会被添加到队列刷新函数(flushSchedulerQueue)的后面（放入微任务队列），这样等队列内部的更新函数都执行完毕，所有DOM操作也就结束了，callback自然能够获取到最新的DOM值。


---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接：

* [查看Vue源码地址](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/renderer.ts#L1549-L1550)

