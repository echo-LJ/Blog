---
title: vue项目中引入全局scss变量
date: 2020-11-20 14:34:56
tags:
---
vue项目中设置全局引入scss，使每个组件都可以使用变量

在Vue项目中使用scss,如果写了一套完整的有变量的scss文件。那么就需要全局引入，这样在每个组件中使用。
可以在mian.js全局引入，下面是使用方法。

1： 安装node-sass、sass-loader、style-loader
```
npm install node-sass --save-dev
npm install sass-loader --save-dev
npm install style-loader --save-dev 
```

2： 安装sass-resources-loader （如果不安装的话变量会报错）

```
npm install sass-resources-loader --save-dev
```

3： 修改build中的utils.js
```
scss: generateLoaders('sass'),

修改成:

scss: generateLoaders('sass').concat(
  {
    loader: 'sass-resources-loader',
    options: {
      resources: path.resolve(__dirname, '../src/assets/main.scss') //这里写自己的文件路径
    }
  }
)
```
在main.js里面引入即可。 或者是不在main.js引入，单独在组件引入也可以。 在组件中的style标签添加lang="scss"。
