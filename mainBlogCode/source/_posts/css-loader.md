---
title: css-loader
date: 2023-04-24 14:31:40
tags:
---

css-loader 是 Webpack 的模块化构建工具中的一个 loader。

### css-loader作用

主要功能是将CSS文件转化为JavaScript模块, 通过 css-loader，webpack 可以将 CSS 文件作为一个模块引入到 JavaScript 中，从而实现样式的`模块化管理`.


* **将 CSS 文件作为模块引入到 JavaScript 中;**
* **将 CSS 文件解析成 AST(抽象语法树)；**
* **处理文件中的@import、url()等链接；**
* **支持样式模块化，可以为样式添加局部作用域；**

#### 将 CSS 文件作为模块引入到 JavaScript 中

在前端项目中，引入多个css样式文件

**传统方案:**在HTML中使用`<link>`标签,在js中通过操作DOM来修改样式，这种方式存在一些问题：
* 页面加载速度慢，存在阻塞；
* CSS无法使用模块化管理，不利于复用和维护；

**webpack+css-loader:**  webpack利用css-loader将css转换成JS 模块，并将模块引入到 JavaScript 文件中。在运行时，webpack 会通过 js 代码注入 style 标签来实现样式的展示。
* webpack中添加对应的css-loader配置
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          'style-loader',
          'css-loader',
        ],
      },
    ],
  },
};
```
* 在 JavaScript 文件中引入 CSS 文件：