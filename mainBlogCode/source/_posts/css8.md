---
title: CSS-position
date: 2023-05-09 14:03:09
tags: CSS
---

<meta name="referrer" content="no-referrer"/>

### position:fixed一定是相对于浏览器窗口进行定位么？

`position:fixed`的元素会被移出正常文档流，并不为元素预留空间，而是通过指定元素相对于`屏幕视口（viewport）`的位置来指定元素位置，元素的位置在屏幕滚动时不会改变。fixed 属性会创建新的层叠上下文。

**特殊情况：**当元素祖先的 `transform`, `perspective` 或 `filter` 属性非 none 时，容器由视口改为该祖先。

在这种情况下，位置偏移值（top，right，bottom，left）会相对于最近的祖先元素进行定位，如果祖先元素的定位方式为 static，则会将该祖先元素设置为 transform: translateZ(0);，以创建新的定位上下文。祖先元素应用的 CSS 变换将影响所有其子元素的位置和空间。因此，position:fixed 相对于祖先元素进行定位。

* [perspective](https://developer.mozilla.org/zh-CN/docs/Web/CSS/perspective) 用于为元素创建3D空间中的透视效果。使用perspective属性为元素设置透视距离
```
perspective:500px
```
* [filter](https://developer.mozilla.org/zh-CN/docs/Web/CSS/filter)调整图像或文本的颜色、亮度、对比度、饱和度和模糊度.

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

