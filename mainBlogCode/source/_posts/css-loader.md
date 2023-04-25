---
title: style-loader、css-loader、postcss-loader、file-loader、 url-loader
date: 2023-04-24 14:31:40
tags: webpack
---

<meta name="referrer" content="no-referrer"/>

css-loader 是 Webpack 的模块化构建工具中的一个 loader。

### css-loader作用

主要功能是将CSS文件转化为JavaScript模块, 通过 css-loader，webpack 可以将 CSS 文件作为一个模块引入到 JavaScript 中，从而实现样式的`模块化管理`.


* **将 CSS 文件作为模块引入到 JavaScript 中;**
* **将 CSS 文件解析成 AST(抽象语法树) - postcss-loader的能力**
* **处理文件中的@import、url()等链接；**
* **支持样式模块化，可以为样式添加局部作用域；**

### 1、将 CSS 文件作为模块引入到 JavaScript 中

在前端项目中，引入多个css样式文件

**传统方案:**在HTML中使用`<link>`标签,在js中通过操作DOM来修改样式，这种方式存在一些问题：
* 页面加载速度慢，存在阻塞；
* CSS无法使用模块化管理，不利于复用和维护；

**webpack+css-loader:**  `webpack利用css-loader将css转换成JS 模块，并将模块引入到 JavaScript 文件中。在运行时，webpack 会通过 js 代码注入 style 标签来实现样式的展示。`
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
```
import './style.css';
```
### 2、postcss-loader: 将 CSS 文件解析成 AST(抽象语法树) 

`postcss-loader`的功能是实现各种 CSS 预处理、后处理的功能，例如添加浏览器前缀、使用变量、嵌套规则等等

`postcss-loader`的原理是把 CSS 代码解析成抽象语法树结构（Abstract Syntax Tree，AST），再交由其内部`插件`来将 AST 格式转换为 CSS 格式、进行压缩等.

`postcss-loader`支持 Source Map。通过开启 Source Map 选项，可以在浏览器的开发者工具中准确的显示样式所在的源文件和行号，方便进行调试。

例如`Autoprefixer插件:`添加浏览器前缀到CSS内容里.

插件基于 CSS 代码的 AST 所能进行的操作是多种多样的,例如：

1、可以支持变量和混入（mixin）
2、增加浏览器相关的声明前缀
3、使用将来的 CSS 规范的样式规则转译（transpile）成当前的 CSS 规范支持的格式
4、等等



### 3、处理文件中的@import、url()等链接；
(css-loader)[https://webpack.docschina.org/loaders/css-loader/] 官方API: css-loader 会对 @import 和 url() 进行处理，就像 js 解析 import/require() 一样。


**处理 url() 导入的图片等资源链接**

* css-loader 会将这些链接转换为 require() 导入的形式，webpack在解析的时候，通过require()函数，会自动处理图片等静态资源，通过`file-loader`并将它们打包到输出目录中.

```
// 转换前
/* main.css */
.logo {
  background-image: url('./logo.png');
}

//css-loader转换后

// main.css.js

exports.locals = {
  logo: 'main_logo_hash'
};

exports.use = function() {
  var styles = [];
  styles.push(".main_logo_hash {\n  background-image: url(" + require("./logo.png") + ");\n}");
  return styles;
};
```

**处理 @import 导入的 CSS 文件**

当 CSS 文件中通过 @import 导入其他 CSS 文件时，css-loader 会将被导入的 CSS 文件一起打包到 JavaScript 中，并返回代表导入后的样式的 JavaScript 对象。这可以`减少网络请求次数`，`提高页面加载速度`。

```
/* reset.css */
body {
  margin: 0;
}

/* main.css */
@import 'reset.css';
.container {
  width: 100%;
}
// 转换后
// main.css.js

exports.locals = {
  container: 'main_container_hash'
};

exports.use = function() {
  var styles = [];
  styles.push(require("./reset.css.js"));
  return styles;
};
```


### 4、支持样式模块化，可以为样式添加局部作用域
css-loader 支持样式模块化，可以为每个 CSS 文件添加局部作用域，避免样式冲突等问题。
css-loader 会为每个模块生成一个独一无二的类名，并在模块样式中引用该类名，从而实现样式作用域的隔离。

### file-loader

file-loader是处理静态资源文件的地址的，解决图片引入问题，将图片原封不动拷贝到指定目录，默认为dist

#### url-loader

url-loader 与file-loader类似，除此以外它还可以设置图片小于limit值时，将小体积的图片等静态资源转换为 base64 编码插入到 CSS 文件或 HTML 文件中，


```
// webpack中url-loader的配置方式
{
            loader: 'url-loader',
            options: {
              limit: 1024 * 10, // 小于 10KB 的图片转换为 base64 编码
              name: 'images/[name].[hash:8].[ext]' // 输出名称格式
            }
          }
```


上述代码配置了 url-loader，并将该 loader 的参数配置在 options 属性中。其中，我们指定了 limit 参数，表示小于 10KB 的图片会被转换为 base64 编码，而大于该值的图片则会被处理成文件格式并保存到输出目录。我们还指定了 name 参数，该参数具体含义请参考文档。


### style-loader

style-loader 的作用是把 CSS 插入到 DOM 中，就是处理 css-loader 导出的模块数组，然后将样式通过 style 标签或者其他形式插入到 DOM 中。
### 多个loader配合使用时，处理顺序是：从下到上，从右到左 的顺序


### webpack配置loader
```
var path = require('path');

module.exports = {
context: path.join(__dirname, 'app'),
entry: './app',
output: {
path: path.join(__dirname, 'dist'),
filename: 'bundle.js'
},
module: {
loaders: [
{
test: /\.css$/,
loader: "style-loader!css-loader!postcss-loader!less-loader"
}
]
},
postcss: function () {
return [require('autoprefixer')];
}
}
```

参考链接：

* [透过现象看webpack处理css文件中图片路径转换的具体过程](https://juejin.cn/post/6844904057568493582)
* [Webpack进阶 :less-loader、css-loader、style-loader实现原理](https://juejin.cn/post/6944668149849522213)
* [css-loader](https://webpack.docschina.org/loaders/css-loader/)
* [浅析css-loader和style-loader的作用、css-loader和style-loader是如何配合使用的、less-loader和css-loader和style-loader的实现原理](https://www.cnblogs.com/goloving/p/14793201.html)
* [使用PostCSS进行CSS处理](https://www.yelcat.cc/index.php/archives/1123/)
