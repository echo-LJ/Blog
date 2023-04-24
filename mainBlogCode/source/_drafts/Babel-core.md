---
title: Babel-@babel/core源码详解
date: 2023-04-24 14:10:07
tags: tool
---

<meta name="referrer" content="no-referrer"/>

`@babel/core`是babel的核心，主要起`串联的作用`，功能包括`加载配置`、调用@babel/parser`解析AST`、调用@babel/traverse`遍历并操作AST`、调用@babel/generator`生成代码`。


参考链接：
* (@babel/core)[https://juejin.cn/post/7041068341754069006#heading-2]
