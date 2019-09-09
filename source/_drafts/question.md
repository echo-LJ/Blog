---
title: question
tags:
---

* `webTitle`: 浏览器解析过程?
* `webTitle`: HTTP response报文结构是怎样的
* `vueSourceCoding`:看一下这个https://www.cnblogs.com/zhuzhenwei918/p/9266407.html 百度搜索一下`vue serverPrefetch`& https://zhuanlan.zhihu.com/p/25936718

https://ssr.vuejs.org/zh/
https://cn.vuejs.org/v2/guide/ssr.html

* 将华清的组件库和可视化大屏整理成组件库和项目汇总

* 看一下process.env.NODE_ENV是怎么回事 https://www.jb51.net/article/126838.htm

* 前端express模拟数据库。

* 看一下媛媛的代理

* 整理一下storybook组件库& 思路

* 发布组件到npm上
* 整理一下发布npm组件库的思路


* 看一下前端工程化

* js实现虚拟的dom树
* vue源码数据怎么触发数据更新
* 了解骨架屏

* hexo发布的文章怎么折叠起来
* hexo怎么将草稿变成发布文章

 
* 请求接口会出现unhandled promise rejection，更改一下http.js

* 了解一下deep,样式穿透。


学习顺序：

* 看一下前端面试题 打卡 1天
* 看一下vue源码    打卡 2天 https://zhuanlan.zhihu.com/p/36030216
* 了解 axios： 打卡 1天(完成)
* 了解proxy: 打卡 1天
* 了解一下webpack相关配置   打卡 0天
* 了解一下express   打卡 0天
* 看一下前端工程化  打卡 0天



* axios 封装思路参照 blog 文件中的draft ->axios.md
* request > index.js作用： 将所有分装的接口暴露出来。 
* 在main.js文件中引入即可使用。
```
import api from '@/request/index';
Vue.use(api);
```
> axios中所有的依赖通过npm install 安装即可。
