---
title: cssTitle
tags:
---


#### 1. css中的border:none和border:0px有什么区别？-156

* none是没有边框. 0px是边框的宽度为0px.
* 举例： 
      从border: 10px;过渡到border: none;是不会有动画的;
      从border: 10px;过渡到border: 0px;是可以有动画的。
* 浏览器解析与否
    1. border为0的情况下虽然看不见,但是浏览器会对border进行渲染,还是占据内存的.
    而border为none时浏览器不会对其渲染,所以不会占据内存
    2. border为none的兼容差 在老IE浏览器上还是会有边框
    而border为0的时候是可以隐藏的。

问题参考链接： https://github.com/haizlin/fe-interview/issues/1222