---
title:  为什么const、let定义的变量不能二次定义?
date: 2022-10-20 09:25:53
tags: JS
---

<meta name="referrer" content="no-referrer"/>


## 概述

 在ES6规范有一个词叫做`Global Enviroment Records(全局环境变量记录)`，它里面包含两个内容，一个是`Object Enviroment Record`，另一个是`Declarative Enviroment Record`。
 * 函数声明和使用var声明的变量会添加进入`Object Enviroment Record`中。
 * 使用let声明和使用const声明的变量会添加入`Declarative Enviroment Record`中。
 
下面是ECMAscript规范中对var,let,const的一些约束:

![ecma.png](https://upload-images.jianshu.io/upload_images/11846892-0fd8434483ea3b10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* 使用var声明时，V8引擎只会检查`Declarative Enviroment Record`中是否有该变量，如果有，就会报错，否则将该变量添加入`Object Enviroment Record`中。
* 使用let和const声明时，引擎会同时检查`Object Enviroment Record`和`Declarative Enviroment Record`，如果有，则报错，否则将将变量添加入`Declarative Enviroment Record`中。

这就解释了为什么使用var声明的变量可以重复声明，而是用let和const声明的变量不可以重复声明。

### 参考链接：

* [为什么let和const不能重复声明？为什么let和const存在暂时性死区？](https://juejin.cn/post/6950193996538839077)
