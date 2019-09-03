---
title: jsTitle
date: 2019-09-03 20:44:24
tags:
---

# js相关面试题整理
Author: Echo
Time: 2019-09-03 20:44:24

## window.load 和$(document).ready() 区别

* 执行时间

window.onload必须等到页面内包括图片的所有元素加载完毕后才能执行。 

$(document).ready()是DOM结构绘制完毕后就执行，不必等到加载完毕。

* 执行的次数不同

window.onload不能同时编写多个，如果有多个window.onload方法，只会执行一个。

$(document).ready()可以同时编写多个，并且都可以得到执行 

* 简化写法 

window.onload没有简化写法 

$(document).ready(function(){})可以简写成$(function(){});