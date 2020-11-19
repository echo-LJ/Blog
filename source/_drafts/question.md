---
title: question
tags:
---
## 安装全局包报没权限
解决方法，运行：sudo npm install -g @vue/cli
## 打开/usr/local
1.Mac下/usr/local目录默认是对于Finder是隐藏,如果需要到/usr/local下去,打开Finder,然后使用command+shift+G,在弹出的目录中填写/usr/local就可以了。
## 关联本地文件夹到github项目
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



回退到某个commit
第一步： git clone [git-url] -b [branch-name]

第二步：git reset --hard [commit-number]
```
* `webTitle`: 浏览器解析过程?
* `webTitle`: HTTP response报文结构是怎样的
* `vueSourceCoding`:看一下这个https://www.cnblogs.com/zhuzhenwei918/p/9266407.html 百度搜索一下`vue serverPrefetch`& https://zhuanlan.zhihu.com/p/25936718

#### 在jd的电脑提交代码到github上需要重定向密钥：

eval $(ssh-agent -s)
windows:ssh-add ~/.ssh/lujing163
mac: ssh-add ~/.ssh/github
wx8e506d9ea5a0a3de
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
* 单页面和多页面应用的区别。
* 一个项目怎么支持多入口


* React Intl是什么原理？  在react项目中实现国际化
参考链接：https://www.jianshu.com/p/c57e000c2bbf




react 生命周期函数图片
<!-- ![react.png](https://github.com/bailicangdu/pxq/raw/master/screenshot/react-lifecycle.png)
![REDUX.png](值得注意的是connect，Provider，mapStateToProps,mapDispatchToProps是react-redux提供的，redux本身和react没有半毛钱关系，它只是数据处理中心，没有和react产生任何耦合，是react-redux让它们联系在一起) -->


react https://www.ru23.com/note/55bd244d.html 看一下
https://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html： 汤姆大叔
https://www.cnblogs.com/TankXiao/archive/2012/02/06/2337728.html#summaryview fiddler  /charles



# 项目中遇到的问题
* 安全工单报高级警告：一般是项目中依赖包版本过低。需要更新到指定版本以上。
解决办法：正常情况下是直接更新packjson中对应的依赖包就可以啦，但是有些工单所警告的依赖包是其他顶层依赖的深层依赖，在packjson 中搜索不到，处理办法是在packjson-lock中搜索警告的依赖包名称，因为这个底层依赖包可能 有很多 顶层依赖引用，所以需要针对报错的依赖进行更新，只能更新对应的顶层依赖，我当时是先更新到npm上介绍的最新版本，后来发现打包的时候报错。然后我将当前依赖包更新到当前版本的最新版本，解决了这个问题。

## 请问你在项目中做了什么？
这个项目为了seo和加快首屏加载速度，进行了一个ssr渲染，
* 然后用了很多性能优化实例：
1. 懒加载
2. 多平台适配方案
3. 中间层

* 有难度的任务
1. 进行了技术攻坚
2. 打包速度过慢，优化打包操作，加快打包速度

* 基础设施建设
1. 对项目做了初始化模版
2. 封装了组件库
3. express + mock
4. 二次封装了axios
5. 针对路由进行了菜单和权限配置
6. 实现了动态路由
7. 定义前端规范md

...

github中vue-demo项目视频地址：https://www.youtube.com/watch?v=Fa4cRMaTDUI


* react组件库基础搭建参考地址：https://github.com/c10342/lin-ui

* ssr : nuxt框架