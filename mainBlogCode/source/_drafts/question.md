---
title: question
tags:
---


## 待做事情？
* 丰富简历，将前端工程化中标红的句子丰富到简历中。
  自行编写后端服务，实现数据的适配，应用场景包括接口的整合编排、字段裁剪；
  实现SSR（服务端渲染直出）技术，达到提升首屏性能以及 SEO 友好的目的；
* 了解 MvvM 和 Node.js的作用和带来的消耗成本。
* 将npm的小技巧和为什么开发私服源整理出文档
* Vue CLI 3配置svg-sprite-loader与svgo-loader： https://juejin.cn/post/6854573215646875655

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


Host github.com
HostName github.com
User xxx@163.com
IdentityFile ~/.ssh/id_rsa_github

Host git.xxx.net  //gitlab域名
HostName git.xxx.net //gitlab域名
User xxx@xxxx //gitlab邮箱
IdentityFile ~/.ssh/id_rsa


* `webTitle`: 浏览器解析过程?
* `webTitle`: HTTP response报文结构是怎样的
* `vueSourceCoding`:看一下这个https://www.cnblogs.com/zhuzhenwei918/p/9266407.html 百度搜索一下`vue serverPrefetch`& https://zhuanlan.zhihu.com/p/25936718

#### 在jd的电脑提交代码到github上需要重定向密钥：

eval $(ssh-agent -s)
mac: ssh-add ~/.ssh/my-mac 
longfor mac: ssh-add ~/.ssh/github 
ssh -T git@github.com
https://blog.csdn.net/weixin_45434534/article/details/104567870
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
参考链接:https://www.jianshu.com/p/c57e000c2bbf




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





----
// 下面的还没有看
关于建站安全防护

要做好防XSS、CSRF、SQL注入攻击.DDOS攻击。

XSS概念:

译为跨站脚本攻击,具体是指攻击者在Web页面里插入恶意Script脚本，当用户浏览该网页时，Script代码会被执行，从而进行恶意攻击。
XSS预防:

关键cookie字段设置httpOnly
输入检查,特殊字符 < > / &等,对其进行转义后存储
CSRF概念：

本质上讲，是黑客将一个http接口中需要传递的所有参数都预测出来，然后不管以什么方式，他都可以根据他的目的来任意调用你的接口，对服务器实现CURD。
CSRF 预防：

使用验证码，更高级用图灵测试
SQL概念：

通常没有任何过滤，直接把参数存放到了SQL语句当中
SQL预防：

根本上防止SQL注入的方法，就是参数化查询或者做词法分析。
DDOS概念：

利用木桶原理，寻找利用系统应用的瓶颈；阻塞和耗尽；当前问题：用户的带宽小于攻击的规模，噪声访问带宽成为木桶的短板。
DDOS预防：用软硬件结合的方式来防御是最有效的
25.对前端工程化的理解

1.开发规范
2.模块化开发
3.组件化开发
4.组件仓库
5.性能优化
6.项目部署
7.开发流程
8.开发工具
26.AMD和CMD是什么？它们的区别有哪些？

AMD 和 CMD 是二种模块定义规范。现在都使用模块化编程，AMD，异步模块定义；CMD，通用模块定义。AMD依赖前置，CMD依赖就近。CMD的 API 职责单一，没有全局require，AMD的一个API可以多用。

27.MVC 

mvc是模型(model)－视图(view)－控制器(controller)的缩写，一种软件设计典范使用MVC的目的是将M和V的实现代码分离，从而使同一个程序可以使用不同的表现形式。MVC对应Html，CSS，js。


28.你如何对网站的文件和资源进行优化？

期待的解决方案包括：文件合并文件最小化/文件压缩使用CDN托管缓存的使用（多个域名来提供缓存）其他。
29.如果网页内容需要支持多语言，你会怎么做？

1.应用字符集的选择，选择UTF-8编码
2.语言书写习惯&导航结构
3.数据库驱动型网站
30.如果设计中使用了非标准的字体，你该如何去实现？

所谓的标准字体是多数机器上都会有的，或者即使没有也可以由默认字体替代的字体。
方法：
用图片代替
web fonts在线字库，如Google Webfonts，Typekit等等;http://www.chinaz.com/free/20…；
@font-face，Webfonts(字体服务例如：Google Webfonts，Typekit等等。)
31.如何自学一门新编程语言

(1)了解背景知识：历史、现状、特点、应用领域、发展趋势
(2)搭建开发环境，编写HelloWorld
(3)声明变量和常量
(4)数据类型
(5)运算符
(6)逻辑结构
(7)通用小程序
(8)函数和对象
(9)第三方库、组件、框架
(10)实用项目
32.什么是哈希表？

散列表（也叫哈希表），是根据关键码值直接进行访问的数据结构。也就是说，它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度。这个映射函数叫做散列函数，存放记录的数组叫做散列表。

33.静态网页和动态网页区别：

静态: 网页内容任何人在任何时间访问都是不变的

动态: 网页内容不同人在不同时间访问可能是不同的

34.SQL语句的分类

DDL:数据定义语句 CREATE/DROP/ALTER…
DCL:数据控制语句 GRANT…
DML:操作操作语句 INSERT/UPDATE/DELETE
DQL:查询语句 SELECT
35.什么是弹性布局？

解决某元素中“子元素”的布局方式，为布局提供最大的灵活性。

设为 flex 布局以后，子元素的 float、clear 和vertical-align属性将失效!!!

display:flex; 属性align-self 定义子元素的位置。

36.编写响应式？

1.声明viewport元标签
2.使用流式布局
3.所有容器使用相对尺寸，不用绝对尺寸
4.（最重要原则）使用CSS3 Media Query技术
37.常见的浏览器兼容问题？

1.不同浏览器的标签默认的内.外补丁不同。*{margin:0;padding:0;}
2.图片默认有间距使用float属性为img布局
3.居中问题（而FF默认为左对齐）margin: 0 auto;
4.CSS 兼容前缀 -ms- IE、-moz- Firefox、-o- Opera、-webkit- Chrome、
5.使用CSS Hack 如： +:IE6,7的前缀、-:IE6的前缀
38.H5新特性：

(1)Canvas绘图
(2)SVG绘图
(3)地理定位
(4)Web Worker
web worker 是运行在后台的 JS，独立于其他脚本，不会影响页面的性能。
(5)Web Storage
1.Cookie技术 （ 兼容性好,数据不能超4kb,操作复杂）
2.（兼容性差,数据8MB,操作简单）sessionStorage
3.localStorage
(6)Web Socket
WebSocket协议是基于TCP的一种新的网络协议。它实现了浏览器与服务器全双工(full-duplex)通信——允许服务器主动发送信息给客户端。
39.C3新特性：

1.复杂的选择器
2.弹性布局
3.动画
40.什么是typescript

1.它是JavaScript的一个超集，而且本质上向这个语言添加了可选的静态类型和基于类的面向对象编程。
2.TypeScript扩展了JavaScript的语法，所以任何现有的JavaScript程序可以不加改变的在TypeScript下工作。TypeScript是为大型应用之开发而设计，而编译时它产生 JavaScript 以确保兼容性。
41.三大框架的区别：

Angular带有比较强的排它性的
React主张是函数式编程的理念，侵入性没有Angular那么强，主要因为它是软性侵入。
Vue 渐进式的
42.spa应用

优点：用户体验好 、良好的前后端分离。

缺点：
1.不利于SEO。
2.初次加载耗时相对增多。
3.导航不可用，如果一定要导航需要自行实现前进、后退。
43.什么是模块化编程？

每个模块内部，module变量代表当前模块。

这个变量是一个对象，它的exports属性（即module.exports）是对外的接口。加载某个模块，其实是加载该模块的module.exports属性。

44.性能优化？

1.使用 CDN
2.图片懒加载
3.使用外部 JavaScript 和 CSS
4.压缩 JavaScript 、 CSS 、字体、图片等
5.优化 CSS Sprite
6.减少 HTTP 请求数
7.减少 DNS 查询
8.减少 DOM 元素数量
9.减少 DOM 操作
10.把脚本放在页面底部
可以看看这篇文章 关于前端优化的一些方案
45.什么是HTTP协议：

HTTP是一个客户端和服务器端请求和应答的标准（TCP）。
HTTP1.1 和 2.0协议的区别：
HTTP1.1不支持 header 数据的压缩，而2.0支持



①②③④⑤⑥⑦⑧⑨⑩⑪⑫⑬⑭⑮⑯⑰⑱⑲⑳㉑㉒㉓㉔㉕㉖㉗㉘㉙㉚㉛㉜㉝㉞㉟㊱㊲㊳㊴㊵㊶㊷㊸㊹㊺㊻㊼㊽㊾㊿




