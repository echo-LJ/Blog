---
title: CSS盒模型
date: 2023-05-05 13:45:19
tags: CSS
---

<meta name="referrer" content="no-referrer"/>


### 什么是盒模型？

当对一个文档进行布局(laylout)的时候，浏览器的渲染引擎会根据标准之一的CSS盒模型，将所有元素标示为一个矩形盒子。

`盒模型`是指HTML/CSS的一个概念：描述网页上每个元素的基本结构和样式，每个元素都被看作是一个矩形的盒子，包括了`内容区域content`、`内边距padding`、`边框border`、`外边距margin`



* **content:** 盒子内包含的实际内容.
* **padding:** 内容与边框之间的空白区域，可以用来控制盒子内容与边框的距离,取值不能为负，受盒子的background属性影响.
* **border:** 边框，围绕元素内容的内边距的一条或多条线，可以设置颜色、样式和宽度等属性.
* **margin:** 边框与其他盒子之间的空白区域，可以用来控制盒子与其他盒子的距离。

### 盒模型区分？

* `W3C 标准盒子模型`  盒子总宽度 = width（content） + padding + border + margin;
* `IE 怪异盒子模型`  盒子总高度 = width(content、padding、border) + margin

标准盒模型的盒子大小可控，不会因为内外边距、边框的变化而影响盒子本身的大小，适配响应式布局更方便。
怪异盒子模型如果需要控制盒子内部的实际大小，需要单独计算内边距和边框的大小，如果采用响应式布局，则需要每个盒子都手动调整。

### Box-sizing


CSS 中的 `box-sizing` 属性定义了引擎应该如何计算一个元素的总宽度和总高度
 
* `content-box`: 元素的 width/height 不包含padding，border，与标准盒子模型表现一致
* `border-box`: 元素的 width/height 包含 padding，border，与怪异盒子模型表现一致
* `inherit:`默认属性值，应该从父元素继承

```
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️
