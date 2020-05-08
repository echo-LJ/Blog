---
title: question
tags:
---
关联本地文件夹到github项目
```
git init
git remote add origin https://自己的仓库url地址
git status
git add .
git commit -m '[提交内容的描述]'
先 push，以后才能 pull：git push --set-upstream origin master
再 git pull
撤销 git init 命令：
rm -rf .git

撤销 git add . 命令：
git rm -rf --cached .

```
* `webTitle`: 浏览器解析过程?
* `webTitle`: HTTP response报文结构是怎样的
* `vueSourceCoding`:看一下这个https://www.cnblogs.com/zhuzhenwei918/p/9266407.html 百度搜索一下`vue serverPrefetch`& https://zhuanlan.zhihu.com/p/25936718

####### 在jd的电脑提交代码到github上需要重定向密钥：

eval $(ssh-agent -s)
windows:ssh-add ~/.ssh/lujing163
mac: ssh-add ~/.ssh/github
https://ssr.vuejs.org/zh/
https://cn.vuejs.org/v2/guide/ssr.html
* 看一下process.env.NODE_ENV是怎么回事 https://www.jb51.net/article/126838.htm


* mac:总结一下github中怎么发布的doc和myzone这两个项目
* mac:将华清的组件库和可视化大屏整理成组件库和项目汇总&发布组件到npm上



* 看一下前端工程化（媛媛之前分享）
* 了解骨架屏
* 了解一下typeScript
* 了解一下webpack
* 了解一下node
* 了解一下deep,样式穿透。
* 了解一下github中的vue+express&写一下思路。
* 看一下redux 中dispatch怎么实现换执行触发reducer
* 看一下redux 中connect
* 熟悉一下async await ,什么时候会用到


* React Intl是什么原理？  在react项目中实现国际化
参考链接：https://www.jianshu.com/p/c57e000c2bbf




react 生命周期函数图片
<!-- ![react.png](https://github.com/bailicangdu/pxq/raw/master/screenshot/react-lifecycle.png)
![REDUX.png](值得注意的是connect，Provider，mapStateToProps,mapDispatchToProps是react-redux提供的，redux本身和react没有半毛钱关系，它只是数据处理中心，没有和react产生任何耦合，是react-redux让它们联系在一起) -->


react https://www.ru23.com/note/55bd244d.html 看一下
https://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html： 汤姆大叔
https://www.cnblogs.com/TankXiao/archive/2012/02/06/2337728.html#summaryview fiddler  /charles


## 讲一下vue的双向绑定原理

在vue2中源码使用的defineProperty来实现双向绑定。
实际上defineProperty它的主要作用是用来给对象中的属性配置操作权限，
例如：
```
writable:false, // 属性值是否可写
  enumerable: false, // 属性值是否可循环
  configurable: false //属性值是否可以delete
```
其中get和set实现了响应式原理。
数据改变之后触发了set方法->set方法触发更新和通知，同时get方法收集依赖->然后更改对应的虚拟dom->重新render.

在这里defineProperty有一个不优雅的地方是，要使用get&set 方法实现读取的存储，必须将属性值重新赋值给新的变量，来实现存储和中转。

而且对数组需要单独处理。
// 数组的双向绑定就是做了一个设计者模式；
// 取出数组的原型链并拷贝;

并且defineProperty只能监听对象的某个属性，不能监听全对象；需要通过for in 循环。

而在vue3中源码使用了的proxy来实现双向绑定原理。proxy相对于defineProperty

proxy的get和set方法中直接可以返回参数target, key, value，可以直接获取到变量，不需要外部变量实现存储和读取。
而且可以监听数组，不需要单独的对数组进行特异性处理。
proxy对原对象进行代理，生成新的代理对象，不会污染原对象。