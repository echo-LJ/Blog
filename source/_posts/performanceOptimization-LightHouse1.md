---
title: 前端性能优化-webpack
date: 2021-06-30 17:11:04
tags:性能优化
---

<meta name="referrer" content="no-referrer"/>

## 前言

Lighthouse是一个Google开源的自动化工具，主要用于改进网络应用（移动端）的质量。目前测试项包括页面性能、PWA、可访问性（无障碍）、最佳实践、SEO。Lighthouse会对各个测试项的结果打分，并给出优化建议，这些打分标准和优化建议可以视为Google的网页最佳实践。

本文省略入门教程，直接从报告分析入手，如有需要，请参考：

## 报告分析

![report](https://upload-images.jianshu.io/upload_images/11846892-239c90568fe34957.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* First Contentful Paint: 渲染第一个元素(文本、图片、canvas...)的时间点
* Time to Interactive: 页面资源加载成功并能响应用户交互的时间点
* Speed Index: 首屏展现时间
* Total Blocking Time: First Contentful Paint 和 Time to Interactive 之间所有时间段的总和，当任务长度超过 50ms 时，以毫秒表示。
* Largest Contentful Paint: 渲染可视区域内最大内容元素的时间点
* Cumulative Layout Shift: 累计布局偏移值
* First Input Delay: 用户第一次在页面进行交互(点击链接、按钮、自定义js事件),到浏览器实际开始处理这个事件的时间

## 改善建议

六个器官，凉了5个😱😱😱😱😱😱❗️

![image.png](https://upload-images.jianshu.io/upload_images/11846892-b1db57dac2d6d14e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 优化手术

#### 优化方案

既然是性能手术,方案就主要以性能指标作为维度,主要分为以下几个点:

1. 视觉稳定性 (Cumulative Layout Shift)
2. 加载情况 (Largest Contentful Paint)
3. TTI (Time to Interactive)
4. TBT (Total Blocking Time)
5. FCP (First Contentful Paint)

#### 优化过程

* 网络负载

* 优化未设置尺寸的图片元素

> 改善建议里提到了一项优先级很高的优化就是为图片元素设置显式的宽度和高度,从而减少布局偏移和改善CLS.

![image.png](https://upload-images.jianshu.io/upload_images/11846892-22cc8cf7c1d8df83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

举个🌰

```
<img src="hello.png" width="50" height="50" alt="Hello World" />
```

* 自定义字体文件加载期间保持可见状态

>改善建议里提到使用CSS font-display属性确保自定义字体文件在加载期间可见.

![image.png](https://upload-images.jianshu.io/upload_images/11846892-157e5330aefe29dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 这是因为网站下载自定义字体文件需要一段时间,而不同浏览器此时的行为是不同的.一些浏览器在加载自定义字体时会隐藏文字,这种称之为FOIT(Flash Of Invisible Text).而一些浏览器会显示降级字体,这种情况称之为FOUT(Flash Of Unstyled Tex).这两种行为会导致"字体闪烁问题",影响视觉稳定性 (CLS).

> 我的处理方法是直接设置font-display:swap;这个属性能确保字体在加载时间可见.虽然还是会引发FOUT,但是相比FOIT,FOUT对视觉稳定性的影响会小一些.

> 更好的方案应该是预加载(preload)字体文件.让字体下载有更高概率赶在FCP之前,从而避免FOIT/FOUT.

```
@font-face {
     font-family: 'Hello-World';
     src: url('../font/Hello-World.otf') format('OpenType');
     /* swap：如果设定的字体还未可用，浏览器将首先使用备用字体显示，当设定的字体加载完成后替换备用字体 */
     font-display:swap;
 }
```
* 避免页面布局发生偏移

![image.png](https://upload-images.jianshu.io/upload_images/11846892-d6da5f93024c2527.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 如果产品中动态插入的元素,这个元素会导致网站整体布局下移.从而造成了较大的布局偏移.可以考虑使该元素脱离文档流,采用固定定位的方式进行展示.从而解决该问题.
* 避免非合成动画

![image.png](https://upload-images.jianshu.io/upload_images/11846892-f90c242b2f8ded6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>改善建议中提到应避免使用非合成动画,非合成动画会使得页面变得混乱并增加CLS.
>注意优化：例如在做CSS动画时尽可能使用transform替代top.
* 替换最大内容绘制元素
![image.png](https://upload-images.jianshu.io/upload_images/11846892-3d4b8817fc7502ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 避免 document.write()
![image.png](https://upload-images.jianshu.io/upload_images/11846892-11248851c17863ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>对于连接速度较慢的用户，通过 `document.write()` 动态注入的外部脚本可以将页面加载延迟数十秒

* 避免过大的DOM元素

![image.png](https://upload-images.jianshu.io/upload_images/11846892-2d4ad5a57369b83a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>一个大的 DOM 会增加内存使用量，导致更长的样式计算，可能会产生很高代价的布局回流。 学到更多。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-2d4ad5a57369b83a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*
参考：https://zhuanlan.zhihu.com/p/29609078
https://jingyan.baidu.com/article/fdffd1f83e5b2eb2e98ca19e.html
https://juejin.cn/post/6917202819351052295

参考链接： 
1. https://juejin.cn/post/6959333330277892133#heading-14
2. https://juejin.cn/post/6917202819351052295