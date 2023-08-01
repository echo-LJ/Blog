---
title: script标签为什么放在body标签的底部(defer、async)
date: 2023-07-20 17:01:24
tags: HTML
---


<meta name="referrer" content="no-referrer"/>

因为浏览器在渲染html的时候是从上到下执行的，当遇到js文件的时候就会停止当前页面的渲染，转而去下载js文件。

如果将script标签放在头部，在文件很大的情况下将导致首屏加载时间延长，影响用户体验。

### 解决办法

将script标签放在body的底部
通过`defer`、`async`属性将js文件转为异步加载
