---
title: 怎么触发BFC？BFC有什么应用场景？
date: 2023-05-10 15:06:58
tags: CSS
---

<meta name="referrer" content="no-referrer"/>


### BFC

BFC 即`块级格式上下文`，它是 Web 中一种常用的页面布局方式。具体来说 BFC 是一个独立的渲染区域，其中的元素按照一定的规则进行布局，并且与 BFC 外部的元素相互独立，不会影响到外部元素的布局。

根据盒模型可知，每个元素都被定义为一个矩形盒子，然而盒子的布局会受到**尺寸，定位，盒子的子元素或兄弟元素，视口的尺寸**等因素决定，所以这里有一个浏览器计算的过程，计算的规则就是由一个叫做**视觉格式化模型**的东西所定义的，BFC 就是来自这个概念，它是 CSS 视觉渲染的一部分，**`用于决定块级盒的布局及浮动相互影响范围的一个区域`**。
### BFC布局规则:

* **1、块级元素会在垂直方向一个接一个的排列，和文档流的排列方式一致。**
BFC 的基础特性，即内部 Block-Level Box 的垂直方向布局方式是顺序排列，不会出现相互重叠的情况
* **2、在 BFC 中上下相邻的两个容器的 `margin`  会重叠，创建新的 BFC 可以避免外边距重叠。**
合并后的 margin 取两个 margin 中的较大值作为最终的 margin。
* **3、 计算 BFC 的高度时，需要计算浮动子元素的高度。**
计算 BFC 的高度时，会考虑浮动元素的高度，从而防止 BFC 区域高度塌陷的问题。
* **4、 BFC 区域不会与浮动的容器发生重叠。**
 BFC 可以阻止浮动元素溢出
* **5、BFC 是独立的容器，容器内部元素不会影响外部元素。**
* **6、每个元素的左 `margin`  值和容器的左 `border`  相接触。**
在 BFC 中，元素的 margin box 不会与 BFC 的包含块（即外部的容器）的 margin box 相重叠。

利用这些特性，我们可以解决以下问题：

-   利用 `4`  和 `6` ，我们可以实现三栏（或两栏）自适应布局。
-   利用 `2` ，我们可以避免 `margin`  重叠问题。
-   利用 `3` ，我们可以避免高度塌陷。


* 开发中的应用
1. 阻止margin重叠
2. 可以包含浮动元素 —— 清除内部浮动(清除浮动的原理是两个 div都位于同一个 BFC 区域之中)
3. 自适应两栏布局
4. 可以阻止元素被浮动元素覆盖


### BFC 作用

可以用于实现页面中的多栏布局、解决清除浮动等问题，是 Web 开发中常用的工具之一。

### 怎么触发BFC？触发条件 (以下任意一条)

* 根元素或包含根元素的元素（例如：HTML）会自动形成 BFC。
* 浮动元素 (float 不是 none) 会形成 BFC。
* 绝对定位元素（position 值为 absolute 或 fixed）会形成 BFC。
* 具有 overflow 值不为 visible 的（即为 auto、scroll、hidden）块级盒子会形成 BFC。
* display的值为table-cell(表格元素)、tabble-caption(表格标题)和inline-block(行内块元素)之一会形成 BFC。

```

inline-block、table-cell、table-caption、table、inline-table、flex、inline-flex、grid、inline-grid
```

* 在IE下, Layout,可通过zoom:1 触发


### BFC应用

#### 自适应两列布局

左列浮动（定宽或不定宽都可以），给右列开启 BFC，利用BFC的特性之一：浮动盒的区域不会和 BFC 重叠。

```
<div>
    <div class="left">浮动元素，无固定宽度</div>
    <div class="right">自适应</div>
</div>

* {
    margin: 0;
    padding: 0;
}
.left {
    float: left;
    height: 200px;
    margin-right: 10px;
    background-color: red;
}
.right {
    overflow: hidden;
    height: 200px;
    background-color: yellow;
}
```
* 将左列设为左浮动，将自身高度塌陷，使得其它块级元素可以和它占据同一行的位置。
* 右列为 div 块级元素，利用其自身的流特性占满整行。
* 右列设置overflow: hidden,触发 BFC 特性，使其自身与左列的浮动元素隔离开，不占满整行。

#### 防止外边距（margin）重叠

兄弟元素之间的外边距重叠

```
* {
    margin: 0;
    padding: 0;
}
.child1 {
    width: 100px;
    height: 100px;
    margin-bottom: 10px;
    background-color: red;
}
.child2 {
    width: 100px;
    height: 100px;
    margin-top: 20px;
    background-color: green;
}
```
两个块级元素，红色 div 距离底部 10px，绿色 div 距离顶部 20px，按道理应该两个块级元素相距 30px 才对，但实际却是取距离较大的一个，即 20px。

**解决办法**
1、创建一个新的 BFC 就不会发生 margin 折叠了。比如我们在他们两个 div 外层再包裹一层容器，加属性 overflow: hidden，触发 BFC，那么两个 div 就不属于同个 BFC 了。
2、统一都使用上边距或者下边距

```
<div>
    <div class="parent">
        <div class="child1"></div>
    </div>
    <div class="parent">
        <div class="child2"></div>
    </div>
</div>
.parent {
    overflow: hidden;
}
```

#### 父子元素的外边距重叠
这种情况存在父元素与其第一个或最后一个子元素之间（嵌套元素）。

如果在父元素与其第一个/最后一个子元素之间不存在`边框`、`内边距`、`行内内容`，也没有创建块格式化上下文、或者清除浮动将两者的外边距 分开，此时子元素的外边距会“溢出”到父元素的外面。
```
<div id="parent">
  <div id="child"></div>
</div>
* {
    margin: 0;
    padding: 0;
}
#parent {
    width: 200px;
    height: 200px;
    background-color: green;
    margin-top: 20px;
}
#child {
    width: 100px;
    height: 100px;
    background-color: red;
    margin-top: 30px;
}
```

![截屏2023-05-10 下午5.03.45.png](https://upload-images.jianshu.io/upload_images/11846892-1116800d5a6bf18e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图，红色的 div 在绿色的 div 内部，且设置了 margin-top 为 30px，但我们发现红色 div 的顶部与绿色 div 顶部重合，并没有距离顶部 30px，而是溢出到父元素的外面计算。即本来父元素距离顶部只有 20px，被子元素溢出影响，外边距重叠，取较大的值，则距离顶部 30px。

**解决办法**

* 给父元素触发 BFC（如添加overflow: hidden）
* 给父元素添加 border
* 给父元素添加 padding

#### 清除浮动解决令父元素高度坍塌的问题

当容器内子元素设置浮动时，脱离了文档流，容器中总父元素高度只有边框部分高度。

```
<div class="parent">
  <div class="child"></div>
</div>
* {
    margin: 0;
    padding: 0;
}
.parent {
    border: 4px solid red;
}
.child {
    float: left;
    width: 200px;
    height: 200px;
    background-color: blue;
}
```
**解决办法**
* 给父元素触发 BFC，使其有 BFC 特性：计算 BFC 的高度时，浮动元素也会参与计算
```
.parent {
    overflow: hidden;
    border: 4px solid red;
}
```

* BFC布局与普通文档流布局区别:



* [HTML/JS/CSS/SVG/画图在线工具](https://c.runoob.com/front-end/61/)

