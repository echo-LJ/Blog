---
title: z-index属性什么情况下会失效？
date: 2023-05-09 10:19:12
tags: CSS
---

<meta name="referrer" content="no-referrer"/>

### z-index作用
z-index属性用于控制定位元素的堆叠顺序，数值越大的元素优先显示。且元素的`position`属性值必须为`relative absolute fixed`之一，

### z-index失效

* 元素不是定位元素
元素的`position`属性值必须为`relative absolute fixed`之一，
* 元素在设置z-index的同时还设置了float浮动。解决：float去除，改为display：inline-block；
* 父元素position为relative时，子元素的z-index失效。解决：父元素position改为absolute或static；
* 在手机端 iOS 13 系统中，-webkit-overflow-scrolling:touch 也会使 z-index 失效，将 touch 换成 unset
* 元素的opacity属性值小于1
如果元素的opacity属性值小于1，那么z-index属性值也可能会失效，因为不透明度值低的元素会被认为是在堆叠上面，而实际上它们可能被其他元素遮盖住
* 元素没有设置背景
如果元素没有背景，z-index属性的设置也可能会失效，因为背景元素可能会覆盖元素。



参考链接：
* [为什么我的 z-index 又不生效了？](https://mp.weixin.qq.com/s?__biz=Mzk0NTI2NDgxNQ==&mid=2247485708&idx=1&sn=e0bbc4755dc078402697a075ff3c0d05&chksm=c31948ccf46ec1da01851d7c8e585e07e0bb5088996cf60bf1ef779b4a54d7c8584a17da4796#rd)