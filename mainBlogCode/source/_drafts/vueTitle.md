---
title: vue相关面试题
date: 2019-09-03 10:10:37
tags: vue
---

Author: Echo
Time: 2019-09-03 10:10:37

##### 2.通过$attrs传递属性
vue api解释说明：
```
vm.$attrs和inheritAttrs
2.4.0 新增
类型：{ [key: string]: string }
只读
详细：
包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。
```
细节使用示例参考链接:https://www.jianshu.com/p/ce8ca875c337

##### 3.通过$listeners传递事件
```
vm.$listeners
2.4.0 新增
类型：{ [key: string]: Function | Array<Function> }
只读
详细：
包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。
```
##### 4.Vue2.6+新全局API：Vue.observable()

### 子组件访问父组件数据（除了props）
* 使用$parent属性
* 通过$attrs传递属性
* Vue2.6+新全局API：Vue.observable()
