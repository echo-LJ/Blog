---
title: Js-basic
date: 2021-07-28 10:39:54
tags: Js
---


<meta name="referrer" content="no-referrer"/>

## 1、 Reflect.ownKeys和Object.keys的区别

* 两者得到的都是对象属性的集合，以数组的形式返回。
* `Object.keys`得到的是对象可枚举属性，且不包括原型上的属性和Symbol属性。
* `Reflect.ownKeys`得到的是对象可枚举属性，包括不可枚举的属性和Symbol属性， 但是拿不到原型上的属性。   