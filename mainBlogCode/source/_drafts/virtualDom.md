---
title: 虚拟dom
date: 2019-09-20 11:05:02
tags: Vue
---

Author: Echo
Time: 2019-09-20 11:05:02

##### 什么是虚拟DOM树？（Virtual DOM）
virtual，指的是对真实DOM的一种模拟。相对于直接操作真实的DOM结构，我们构建一棵虚拟的树，将各种数据和操作直接应用在这棵虚拟的树上，然后再将对虚拟的树的修改应用到真实的DOM结构上。
虚拟DOM树其实就是一个普通的js对象，它是用来描述一段HTML片段的
* 当页面渲染的时候Vue会创建一颗虚拟DOM树
* 当页面发生改变Vue会再创建一颗新的虚拟DOM树
* 前后两颗新旧虚拟DOM树进行对比，Vue通过diff算法，去记录差异的地方
* 将有差异的地方更新到真实的DOM树中
##### 虚拟DOM树有什么用？　
vue中的虚拟DOM树只会重新渲染页面修改的地方，大大减少了对真实DOM树的操作。 -------虚拟DOM树是占内容的，但是可以帮我们提高DOM的性能。

可以这样理解，虚拟DOM树是用空间(虚拟DOM树占空间)换时间(虚拟DOM树可以提高DOM效率)。


参考链接: https://www.cnblogs.com/javascript9527/p/11327924.html

实现一个Virtual DOM算法参考链接: https://www.cnblogs.com/ssw-men/p/10818585.html