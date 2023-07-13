---
title: 说说Vue3的新特性
date: 2023-07-12 16:02:58
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>


[vue3新特性](https://v3-migration.vuejs.org/)
![截屏2023-07-12 下午4.04.44.png](https://upload-images.jianshu.io/upload_images/11846892-68957cba1d2b08cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 思路分析

* Composition API* : 
* SFC Composition API Syntax Sugar (<script setup>)* : SFC Composition API语法糖
* Teleport: 传送门
* Fragments: 片段
* Emits Component Option**: Emits选项
* createRenderer API from @vue/runtime-core to create custom renderers: 自定义渲染器
* SFC State-driven CSS Variables (v-bind in <style>)*:SFC CSS 变量
* SFC <style scoped> can now include global rules or rules that target only slotted content
* Suspense experimental 




## 回答范例

1、api层面Vue3的新特性包括： Composition API、SFC Composition API语法糖、Teleport传送门、Fragments片段、Emits选项、自定义渲染器、SFC CSS 变量、Suspense
2、Vue3.0在框架层面的改进：
**更快：**
* 虚拟DOM重写： 现在的虚拟DOM中会多一些编译优化内容存储，type属性多样性：可能是一个组件，而不仅仅是一个字符串元素
* 编译器优化：静态提升、patchFlags、block等
* 基于Proxy的响应式系统
**更小：**更好的摇树优化
**更容易维护：**TypeScript+模块化
**更容易扩展：**
* 独立的响应式模块： `reactivity包`
* 自定义渲染器

---
总结:大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [Vue在线运行编译工具](https://play.vuejs.org/#eNp9kUFLwzAUx79KfJcqzA3ZbXQDlYF6UFHBSy6je+sy0yQkL7NQ+t19SVn1ILv1/X//l/7SdnDr3PQYERZQhsorRyIgRbeSRjXOehKd8LgTvdh524iCq4U00lTWBBJNqMUy8cviAbW24tN6vb0orqQpZ8NxfBAPhI3TG0KehCj3N6uuy8t9X854yqkyLpI4Xjd2i3opgbkERuVs3IYJUOBX71Q9PQRr2LpLuxIq2zil0b84UqwmYSEySWzDZt9POSMfcXLKqz1WX//kh9CmTMKrx4D+iBJGRhtfIw14/f6MLT+PkM2j5vYZ+IbB6pgch9pdNFvW/tPLto/52ytTf4R1S2jC6VJJNDX73JfA/+P+zNV/defTed6Tpof+B7x8phs=)

