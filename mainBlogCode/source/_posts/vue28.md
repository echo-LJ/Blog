---
title: 子组件可否直接改变父组件的顺序
date: 2023-07-07 13:53:06
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

组件开发过程中有个`单项数据流原则`, 不在子组件中修改父组件的值是常识问题。

官方文档声明:[one-way-data-flow](https://vuejs.org/guide/components/props.html#one-way-data-flow)



## 思路分析
1、讲讲单项数据流原则,表明为何不能这么做
    看过官方文档,one-way-data-flow,单项数据流让数据流向难以理解,出了问题,不好排查
2、举几个常见的场景说说解决方案
3、结合实践讲讲如果需要修改父组件状态应该如何做

## 回答范例

1、所有的props都使得其父子之间形成一个`单向下行绑定:`父级prop的更新会向下流动到子组件中,但是反过来则不行,这样会防止从子组件意外变更父级组件的状态,从而导致应用的数据流向难以追踪。另外,每次父级组件发生变更时,子组件中所有的props都将刷新成最新的值. 这就意味着,不应该在子组件内部改变props的值,如果强行改变,Vue会在控制台中发出警告。

2、实际开发中想要子组件改变父组件数据的场景:
* **子组件接收一个父组件传递的props初始值,子组件希望将其作为本组件的数据使用:** 
处理办法: 子组件定一个data,并将props定义为其初始值。
```
const props = definedProps(['initialCounter'])
const counter = ref(props.initialCounter)
```

* **prop以原始的值传入且需要转换**
处理办法: 子组件通过props定义一个计算属性。

```
const props = definedProps(['size'])
const normalizedSize = computed(() => props.size.trim().toLowerCase())
```
3、如果想要修改父组件的值,子组件派发emit事件,父组件监听事件去修改数据。
---
总结:大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [查看Vue源码地址](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/renderer.ts#L1549-L1550)