---
title: ES6
date: 2021-01-26 18:33:13
tags:
---
## 1. ES6对object类型做了哪些优化和更新
* 对象属性变量式声明
```
let obj = {name, address}
```
* 对象的解构赋值
```
let obj = {name, ...info}
```
* 原型上新增了is()方法
```
用来完善===，NaN === NaN 返回false;
Object.is(NaN,NaN) //true
```
* 原型上新增了assign()方法
用于对象新增属性或多个对象的合并
```
Object.assign(targetObj, obj1, obj2)
```
* 原型上新增了`getOwnPropertyDescriptors()`、`getPrororypeof`、`setPrototypeof`、`Object.keys()`、`Object.values()`、`Object.entries()`方法




220 224 216 219 222

