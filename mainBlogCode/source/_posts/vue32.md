---
title: v-for 中 :key 到底有什么用？
date: 2023-07-13 15:34:35
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

## 思路分析

1、给出结论，key的作用适用于优化patch性能
2、key的必要性
3、实际使用方式
4、总结： 可以从源码层面描述一下vue如何判断两个节点是否相同

## 回答范例

### Vue  v-for 中 :key 到底有什么用？

-  key的作用是唯一标识，为了高效的更新虚拟DOM树，提高查找的效率，一次性定位到要修改的元素（Vue不直接操作真实的DOM树，通过虚拟DOM树就可以重新渲染修改的地方，影藏在背后的原理其实就是 diff 算法）。

-   **（key的必要性）** vue在patch过程中，key是一个必要条件去判断两个node节点是否是相同节点：渲染一组列表时，key往往是唯一标识，所以如果不定义key的话，vue只能认为比较的两个节点是同一个，哪怕它们实际上不是，这导致了`频繁更新元素`，造成了大量的dom更新操作，明显是不可取的,使得整个`patch过程比较低效，影响性能`。

-    **（实际使用方式）** 实际使用中在渲染一组列表时key必须设置，而且必须是唯一标识，应该避免使用数组索引作为key，这可能导致一些隐蔽的bug，如数据错位；vue中在使用相同标签元素过渡`transtion`切换时，也会使用key属性，其目的也是为了让vue可以区分它们，否则vue只会替换其内部属性而不会触发过渡效果。

-  **（源码）**从源码中知道，vue判断两个节点是否相同主要判断两者的key和元素类型，因此不设置key,它的值就是undefined,则可能永远认为是两个相同节点，只能去做更新操作，造成了大量的dom更新操作，不使用可能会导致性能消耗、数据错位。

### [查看相关源码](https://github1s.com/vuejs/core/blob/HEAD/packages/runtime-core/src/renderer.ts)

* 通过patchKeyedChildren方法判断有key的两个元素
* 通过isSameVNodeType判断是否为相同节点： isSameVNodeType通过判断节点的type和key来判断是否为相同节点
---
总结:大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [Vue在线运行编译工具](https://play.vuejs.org/#eNp9kUFLwzAUx79KfJcqzA3ZbXQDlYF6UFHBSy6je+sy0yQkL7NQ+t19SVn1ILv1/X//l/7SdnDr3PQYERZQhsorRyIgRbeSRjXOehKd8LgTvdh524iCq4U00lTWBBJNqMUy8cviAbW24tN6vb0orqQpZ8NxfBAPhI3TG0KehCj3N6uuy8t9X854yqkyLpI4Xjd2i3opgbkERuVs3IYJUOBX71Q9PQRr2LpLuxIq2zil0b84UqwmYSEySWzDZt9POSMfcXLKqz1WX//kh9CmTMKrx4D+iBJGRhtfIw14/f6MLT+PkM2j5vYZ+IbB6pgch9pdNFvW/tPLto/52ytTf4R1S2jC6VJJNDX73JfA/+P+zNV/defTed6Tpof+B7x8phs=)


