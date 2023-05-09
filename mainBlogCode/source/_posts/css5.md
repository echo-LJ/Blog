---
title: 行内元素和块级元素的区别？
date: 2023-05-08 09:47:32
tags: CSS
---

<meta name="referrer" content="no-referrer"/>

###  定义
* **行内元素：** 一个行内元素只占据内容所撑开的空间。
* **块级元素：** 块级元素占据其父元素（容器）的整个水平空间，垂直空间等于其内容高度，因此创建了一个“块”。

### 区别
* 块级元素独占一行，行内元素根据内容宽度决定换行。
* 块级元素可以设置`width`、`height`等属性,行内元素则不生效。
* 块级元素可以设置`padding`、`margin`等属性，行内元素`padding`、`margin` 等属性水平方向有效，竖直方向无效。
* 块级元素可以包含行内元素、块级元素，而行内元素不能包含块级元素。
* 行内元素水平居中`text-align: center`,块级元素水平居中`margin: 0 auto;`
* 行内元素垂直居中

```
height:30px; 
line-height:30px 
```

块级元素垂直居中
```
margin:0 auto;
height:30px;
line-height:30px
```
### 常见的行内元素和块级元素
1、常见的行内元素
```
<span><a><lable><strong><b><small><abbr><button><input><textarea><select><code><img><br><q><i><cite><var><kbd><sub><bdo>
```
2、常见的块级元素
```
<div><p><li><h1><h2><h3><h4><h5><h6><form><header><hr><ol><address><article><aside><audio><canvas><dd><dl><fieldset><section><ul><video>
```


```
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️




