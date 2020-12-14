---
title: 前端工程化工具 ---- module-alias
date: 2020-12-14 14:55:21
tags: 前端工程化
---

使用建议：nodejs的模块引用，使用相对路径，会造成写一堆../，非常不便。
## 一、安装[module-alias](yihttps://www.npmjs.com/package/module-alias)
```
npm install module-alias --save
```
## 二、使用[module-alias](yihttps://www.npmjs.com/package/module-alias)
注：这里@root就是别名，后面引号内的内容就是原路径
```
"_moduleAliases": {
  "@root": ".",
  "@app": "app"
},
```
配置文件引入 `require(‘module-alias/register’);`
```
require('module-alias/register');
import BaseController from '@root/baseController';
```
## 三、ts做特殊映射
在`tsconfig.json`文件中添加相应的映射
```
"compilerOptions": {
    "paths": {
      "@root/*": "["./*"]",
      "@app/*": ["./app/*"],
    }
```
大功告成✌️✌️✌️
