---
title: jsTitle
tags:
---
promise.md
#### promise

jsTitle
#### 宏任务、微任务












#### 用原生JS封装插件的方式有哪些？-156

问题参考链接: https://github.com/haizlin/fe-interview/issues/1223

#### HTML5的video在有的移动端设备无法自动播放？怎么解决？-156

#### 如何让你做一个文件无刷新上传的功能，你有哪些方法可以做到？-156


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