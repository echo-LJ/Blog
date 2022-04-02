---
title: 实现骨架屏的方案
date: 2021-06-09 13:38:41
tags: Skeleton
---

<meta name="referrer" content="no-referrer"/>

#### 前言

现在的前端开发领域，都是前后端分离，前端框架的主流都是SPA，MPA;这就意味着，页面渲染以及等待的白屏时间，成为我们需要解决的问题点；而且大项目，这个问题就尤为突出。

Webpack可以实现按需加载，减少我们首屏需要加载的代码体积；在配合CDN以及一些静态代码（框架，组件库等等...）缓存技术，可以很好的缓解这个加载渲染的时间过长的问题。

即便如此，首屏加载仍然存在加载以及渲染等待时间的问题；

目前，常见的解决方案是使用骨架屏技术，包括很多原生APP，在页面渲染的时候，也会使用骨架屏。

## 一、什么是骨架屏
---
举个🌰：
![1623748626202.jpg](https://upload-images.jianshu.io/upload_images/11846892-b840337fcb559329.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`骨架屏`就是在页面资源尚未加载完成以及渲染尚未完成 时需要先给用户的展示页面大致结构。直到资源加载完成以及渲染完成后 使用渲染的页面。

常用例子： 文章列表，动态列表页等相对比较规则的页面。

实战项目例子： 饿了么h5版本、知乎、Facebook等都有应用。

## 二、什么是骨架屏
---
* 首屏：可以在`index.html`模版中手写骨架屏相关代码。
* 其他页面：可以利用UI提供SVG图
* 使用组件库中的骨架屏组件： **`React: Material-UI中的Skeleton 骨架屏`**、**`Vue: Ant Design of Vue的Skeleton 骨架屏`**等
* 可以使用饿了么团队根据页面样式生成的骨架屏工具**`page-skeleton-webpack-plugin`**

按照用途可以细分为两类

#### 2.1作为SPA中路由切换的loading

需要自己编写骨架屏，推荐两个成熟方便定制的svg组件去定制骨架屏
* [react-content-loader](https://www.npmjs.com/package/react-content-loader)
* [vue-content-loader](https://www.npmjs.com/package/vue-content-loader)

#### 2.2作为首屏渲染的优化（自动化方案）
该方案是饿了么在骨架屏中总结出的一套方案：
* 骨架屏的dom结构和css通过离线生成后构建的时候注入模版中的节点下面。
* 如果你的项目是基于`vue-cli`脚手架构建的，那么饿了么团队的**`page-skeleton-webpack-plugin`**是最佳之选，如果不是，那么可以选择vue-router提供的`vue-server-renderer`。

> page-skeleton-webpack-plugin的原理
通过无头浏览器puppeteer打开要生成骨架屏的页面
等待页面渲染完后注入提取骨架屏的脚本（注意：一定要等页面完全渲染完，不然提取的DOM不完整）
对页面中元素进行删减或增添，对已有元素通过层叠样式进行覆盖，这样达到在不改变页面布局下，隐藏图片和文字，通过样式覆盖，使得其展示为灰色块。然后将修改后的 HTML 和 CSS 样式提取出来生成骨架屏。

**`优势：`**
* 支持多种加载动画
* 针对移动端 web 页面
* 支持多路由
* 可定制化，可以通过配置项对骨架块形状颜色进行配置，同时也可以在预览页面直接修改骨架页面源码
* 几乎可以零配置使用

## 三、骨架屏的具体实现
---
#### 3.1在模版中来实现骨架屏
以vue项目为例，默认情况下，我们的模版`index.html`中有一个id为app的div元素`<div id="app"></div>`,最终应用程序会替换到该div元素。
利用这个特性，在index.html中的id#app的元素内实现骨架屏，程序渲染完成后，就会替换掉骨架屏内容。
也就是首屏，可以在index.html中实现骨架屏样式。如果不手写样式，可以用`base64图片`来代替。（小米商城的移动端页面）
#### 3.2自动生成并自动插入静态骨架屏

* 饿了么开源插件：[page-skeleton-webpack-plugin](https://github.com/ElemeFE/page-skeleton-webpack-plugin/blob/master/docs/i18n/zh_cn.md)
* [vue-skeleton-webpack-plugin](https://github.com/lavas-project/vue-skeleton-webpack-plugin)

## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️



