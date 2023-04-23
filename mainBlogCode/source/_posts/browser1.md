---
title: 浏览器兼容性
date: 2023-04-23 09:42:18
tags: Browser
---

<meta name="referrer" content="no-referrer"/>

### 最新浏览器版本对ES的支持

**一、pc端浏览器对ES的支持情况**

1.chrome：51版起，可支持97%的ES6新特性

2.firefox：53版起，可支持97%的ES6新特性

3.safari：10版起，可支持99%的ES6新特性

4.ie：Edge 15可支持96%的ES6新特性。Edge14可支持93%。（IE7~11基本不支持ES6）

* [查看浏览器兼容性](http://kangax.github.io/compat-table/es6/)

**二、移动端浏览器对ES的支持情况**

1.ios：10.0版起，可支持99%的ES6新特性

2.android：基本不支持ES6新特性（5.1仅支持25%）

**三、服务器对ES的支持情况**

1.node.js：6.5版起，可支持97%的ES6新特性


### 各浏览器兼容ES6的解决方法

把ES6转换为ES5，比较通用的工具有`babel`、`jsx`、`traceur`、`es6-shim`等
