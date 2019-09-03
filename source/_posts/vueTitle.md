---
title: vueTitle
date: 2019-09-03 10:10:37
tags:
---

# vue相关面试题
Author: Echo
Time: 2019-09-03 10:10:37

### 孙子组件或更深层次的组件通信（除了props和vuex的其他用法）
* Vue Event Bus，使用Vue的实例，实现事件的监听和发布，实现组件之间的传递
###### 缺点:
使用在实际的项目操作中发现，如不能实现很好的事件监听与发布的管理，往往容易导致数据流的混乱，在多人协作的项目中，不利于项目的维护
#####1.  父组件使用provide，子组件使用inject
* 通过inject直接访问其两个层级上的数据，其用法与props完全相同，同样可以参数校验等
```
1.provide就相当于加强版父组件prop
2.inject就相当于加强版子组件的props 
```
// 示例代码
```
// 父组件
<template>
	<div>
		{{name}}
	</div>
</template>
 
<script>
export default {
	name: 'father',
  // 传入对象写法
  provide: {
    name: 'bar'
  }
  // 函数写法
	provide () {
    return {
      name: 'foo'
    }
  }
}
</script>

// 孙子组件或者深层次组件
<template>
	<div>
		{{name}}
	</div>
</template>
 
<script>
export default {
	name: 'Grandson',
	inject: [name]
}
</script>
```

###### 缺点
在任意层级都能访问，导致数据变化追踪比较困难。

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
细节使用示例参考链接：https://www.jianshu.com/p/ce8ca875c337

##### 3.通过$listeners传递事件
```
vm.$listeners
2.4.0 新增
类型：{ [key: string]: Function | Array<Function> }
只读
详细：
包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。
```

### 子组件访问父组件数据（除了props）
* 使用$parent属性
* 通过$attrs传递属性
