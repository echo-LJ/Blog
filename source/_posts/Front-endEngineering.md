---
title: 前端工程化
date: 2020-05-27 14:55:06
tags:
---

Author: Echo
Time: 2020-05-27 14:55:06

#### 前言

web应用复杂度的增加，特别是单页面应用的风靡。组件化，工程化，自动化成了前端发展的趋势。
每个前端团队都在打造自己的前端开发体系，这通常是一个东拼西凑，逐渐磨合的过程，在技术发展日新月异的今天，这样的过程真的是不可抽象和复制的么？通过拆解前端开发体系,对前端工程化有所理解。
# I. 前端工程化
---
前端工程本质上是软件工程的一种。软件工程化关注的是性能、稳定性、可用性、可维护性等方面，注重基本的开发效率、运行效率的同时，思考维护效率。一切以这些为目标的工作都是"前端工程化"。

###### 前端工程化面临的问题 ?
 1. 开发成本
大体量：多功能、多页面、多状态、多系统；
大规模：多人甚至多团队合作开发；

  * 提高开发生产效率
  * 降低维护难度

这两个问题的解决方案有两点：

* 制定开发规范，提高团队协作能力；
* 分治。软件工程中有个很重要的概念叫做`模块化开发`其中心思想就是分治。
 2. 部署
从部署角度，要解决的问题主要是资源管理，包括：
* 代码审查
* 压缩打包
* 增量更新
* 单元测试
3. 高性能
CDN 部署、缓存控制、文件指纹、缓存复用、请求合并、按需加载、同步/异步加载、移动端首屏 CSS 内嵌、HTTP 2.0 服务端资源推送。
###### 如何做"前端工程化"？
前端工程化就是为了让前端开发能够“自成体系”，个人认为主要应该从模块化、组件化、规范化、自动化四个方面思考。

### 模块化
---
>简单来说，模块化就是将一个大文件拆分成相互依赖的小文件，再进行统一的拼装和加载。
###### JS的模块化
在ES6之前，JavaScript一直没有模块系统，这对开发大型复杂的前端工程造成了巨大的障碍。对此社区制定了一些模块加载方案，如CommonJS、AMD和CMD等。
现在ES6已经在语言层面上规定了模块系统，完全可以取代现有的CommonJS和AMD规范，而且使用起来相当简洁，并且有静态加载的特性。

* 用 Webpack + Babel 将所有模块打包成一个文件同步加载，也可以搭乘多个chunk异步加载；
* 用 System+Babel 主要是分模块异步加载；
* 用浏览器的<script type="module">加载。
###### CSS的模块化
虽然SASS、LESS、Stylus等预处理器实现了CSS的文件拆分，但没有解决CSS模块化的一个重要问题：选择器的全局污染问题。

按道理，一个模块化的文件应该要隐藏内部作用域，只暴露少量接口给使用者。而按照目前预处理器的方式，导入一个CSS模块后，已存在的样式有被覆盖的风险。虽然重写样式是CSS的一个优势，但这并不利于多人协作。

为了避免全局选择器的冲突，需要制定CSS命名风格：
* BEM风格
* Bootstrap风格
* 团队CSS规范

但是这毕竟是弱约束。所以很赞同一句话：

>与其费尽心思地告诉别人要遵守某种规则，以规避某种痛苦，倒不如从工具层面就消灭这种痛苦。

从工具层面，社区又创造出`Shadow DOM`、`CSS in JS`和`CSS Modules`三种解决方案。

* Shadow DOM是WebComponents的标准。它能解决全局污染问题，但目前很多浏览器不兼容，对我们来说还很久远；
* CSS in JS是彻底抛弃CSS，使用JS或JSON来写样式。这种方法很激进，不能利用现有的CSS技术，而且处理伪类等问题比较困难；
* CSS Modules仍然使用CSS，只是让JS来管理依赖。它能够最大化地结合CSS生态和JS模块化能力，目前来看是最好的解决方案。Vue的`scoped` style也算是一种。
###### 资源的模块化
Webpack的强大之处不仅仅在于它统一了JS的各种模块系统，取代了Browserify、RequireJS、SeaJS的工作。更重要的是它的万能模块加载理念，即所有的资源都可以且也应该模块化。
* `依赖关系单一化`。所有CSS和图片等资源的依赖关系统一走JS路线，无需额外处理CSS预处理器的依赖关系，也不需处理代码迁移时的图片合并、字体图片等路径问题；
* `资源处理集成化`。现在可以用loader对各种资源做各种事情，比如复杂的vue-loader等等；
* `项目结构清晰化`。使用Webpack后，你的项目结构总可以表示成这样的函数： `dest = webpack(src, config)`。
### 组件化
---
从UI拆分下来的每个包含模板(HTML)+样式(CSS)+逻辑(JS)功能完备的结构单元，我们称之为组件。

>`组件化≠模块化`。模块化只是在文件层面上，对代码或资源的拆分；而组件化是在设计层面上，对UI（用户界面）的拆分。

随着 web 应用规模越来越大，模块/组件化开发的需求就显得越来越迫切。模块/组件化开发的核心思想是分治，主要针对的是开发和维护阶段。

1.  Web 应用的组件化开发。[http://blog.jobbole.com/56161/](http://blog.jobbole.com/56161/)
2.  前端组件化开发实践。[http://web.jobbole.com/82689/](http://web.jobbole.com/82689/)
3.  大规模的前端组件化与模块化。[http://www.infoq.com/cn/news/...](http://www.infoq.com/cn/news/2014/04/front-end-modular)

###### 为什么搭建私有组件库?
* 可复用 跨项目可以使用同一套私有组件库
* 方便维护 如需组件调整 只需要修改组件库 不需要跨项目重复修改

##### 添加新组件原则

* 组件应先存在于具体项目中，经过重复验证后再抽象、沉淀到本组件库中
* Vue component 只应负责渲染数据和内部逻辑，尽可能不包含 &dollar;t 国际化、ajax 请求等业务数据和逻辑
* 组件和其他模块应尽可能的搭配单元测试、可运行的 storybook 例子
* 应该在 CHANGELOG 中记录每次增删改的组件信息等

### 规范化
---

规范化其实是工程化中很重要的一个部分，项目初期规范制定的好坏会直接影响到后期的开发质量。

比如：

* ######目录结构的制定
* ######编码规范
    制定一套良好的编码规范可以增强团队开发协作、提高代码质量。
`推荐参考`
**凹凸实验室**打造的[前端代码规范](https://guide.aotu.io/docs/index.html)。
**Javascript Airbnb** 开发规范 [https://github.com/airbnb/jav...](https://github.com/airbnb/javascript)
  * HTML规范
  * CSS规范
  * JS规范
  * 图片规范
  * 命名规范
* ######前后端接口规范
  “基于 Ajax 带来的 SPA 时代”，这种模式下，前后端的分工非常清晰，前后端的关键协作点是 Ajax 接口，引发一个重要问题：前后端的对接界面双方却关注甚少，没有任何接口约定规范情况下各自撸起袖子就是干，导致我们在产品项目开发过程中，前后端的接口联调对接工作量占比在30%-50%左右，甚至会更高。往往前后端接口联调对接及系统间的联调对接都是整个产品项目研发的软肋。

  接口规范主要初衷就是规范约定先行，尽量避免沟通联调产生的不必要的问题，让大家身心愉快地专注于各自擅长的领域。
* ###### 文档规范

* ###### 组件管理

* ###### git分支管理

* ###### commit描述规范

* ###### 定期 CodeReview

### 自动化
---
   前端工程化的很多脏活累活都应该交给自动化工具来完成。需要秉持的一个理念是:

>任何简单机械的重复劳动都应该让机器去完成。

* 图标合并

* 持续继承

* 自动化构建

* 自动化部署

* 自动化测试

# II. 工程化具体方法
---
## 1. 性能优化
浏览器缓存是 Web 性能优化的重要方式。那么浏览器缓存的过程究竟是怎么样的呢？

浏览器缓存主要分为`强强缓存（也称本地缓存）`和`协商缓存（也称弱缓存）`。
 ######强缓存
 *  Expires 是 http1.0 的规范，它的值是一个绝对时间的 GMT 格式的时间字符串。如我现在这个网页的 Expires 值是：expires:Fri, 14 Apr 2017 10:47:02 GMT。这个时间代表这这个资源的失效时间，只要发送请求时间是在 Expires 之前，那么本地缓存始终有效，则在缓存中读取数据。所以这种方式有一个明显的缺点，由于失效的时间是一个绝对时间，所以当服务器与客户端时间偏差较大时，就会导致缓存混乱。如果同时出现 Cache-Control:max-age 和 Expires，那么 max-age 优先级更高。
* Cache-Control 是在 http1.1 中出现的，主要是利用该字段的 max-age 值来进行判断，它是一个相对时间，例如 Cache-Control:max-age=3600，代表着资源的有效期是 3600 秒。cache-control 除了该字段外，还有下面几个比较常用的设置值：

  no-cache：不使用本地缓存。需要使用缓存协商，先与服务器确认返回的响应是否被更改，如果之前的响应中存在 ETag，那么请求的时候会与服务端验证，如果资源未被更改，则可以避免重新下载。

  no-store：直接禁止游览器缓存数据，每次用户请求该资源，都会向服务器发送一个请求，每次都会下载完整的资源。

  public：可以被所有的用户缓存，包括终端用户和 CDN 等中间代理服务器。
  private：只能被终端用户的浏览器缓存，不允许 CDN 等中继缓存服务器对其缓存。
  Cache-Control 与 Expires 可以在服务端配置同时启用，同时启用的时候   Cache-Control 优先级高。
 ######弱缓存/协商缓存
**Etag 和 If-None-Match**

Etag/If-None-Match 返回的是一个校验码。ETag 可以保证每一个资源是唯一的，资源变化都会导致 ETag 变化。服务器根据浏览器上送的 If-None-Match 值来判断是否命中缓存

与 Last-Modified 不一样的是，当服务器返回 304 Not Modified 的响应时，由于 ETag 重新生成过，response header 中还会把这个 ETag 返回，即使这个 ETag 跟之前的没有变化。

**Last-Modify/If-Modify-Since**

浏览器第一次请求一个资源的时候，服务器返回的 header 中会加上 Last-Modify，Last-modify 是一个时间标识该资源的最后修改时间，例如 Last-Modify: Thu,31 Dec 2037 23:59:59 GMT。

当浏览器再次请求该资源时，request 的请求头中会包含 If-Modify-Since，该值为缓存之前返回的 Last-Modify。服务器收到 If-Modify-Since 后，根据资源的最后修改时间判断是否命中缓存。

如果命中缓存，则返回 304，并且不会返回资源内容，并且不会返回 Last-Modify。

Last-Modified 与 ETag 是可以一起使用的，服务器会优先验证 ETag
* * *
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c4678e7b6821f8b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* * *

![image.png](https://upload-images.jianshu.io/upload_images/11846892-4c9053e0fd74c93f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 访问量和性能指标

a.css 的请求，如果每次用户访问页面都要加载，很影响性能，很浪费带宽

![image.png](https://upload-images.jianshu.io/upload_images/11846892-41104d7c231d27e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* * *

利用 304，让浏览器使用本地缓存。304 叫协商缓存，这玩意还是要和服务器通信一次，我们的优化级别是变态级，所以必须彻底灭掉这个请求，变成这样：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-2fd0094493df3b08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
强制浏览器使用本地缓存（cache-control/expires），不要和服务器通信。好了，请求方面的优化已经达到变态级别，那问题来了：你都不让浏览器发资源请求了，这缓存咋更新？

很好，相信有人想到了办法：通过`更新页面中引用的资源路径，让浏览器主动放弃缓存，加载新资源`。好像这样：

![`](https://upload-images.jianshu.io/upload_images/11846892-577a64c93602bf2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

页面引用了 3 个 css，而某次上线只改了其中的 a.css，如果所有链接都更新版本，就会导致 b.css，c.css 的缓存也失效，那岂不是又有浪费了？！

![image.png](https://upload-images.jianshu.io/upload_images/11846892-c3a7badbd331fd6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

重新开启变态模式，我们不难发现，要解决这种问题，必须让 url 的修改与文件内容关联，也就是说，只有文件内容变化，才会导致相应 url 的变更，从而实现文件级别的精确缓存控制。

什么东西与文件内容相关呢？我们会很自然的联想到利用  `数据摘要算法` 对文件求摘要信息，摘要信息与文件内容一一对应，就有了一种可以精确到单个文件粒度的缓存控制依据了。好了，我们把 url 改成带摘要信息的：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-027cf303a23fc22f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现代互联网企业，为了进一步提升网站性能，会把静态资源和动态网页分集群部署，静态资源会被部署到 CDN 节点上，网页中引用的资源也会变成对应的部署路径：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-ae5c4239f5b12df1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


这次发布，同时改了页面结构和样式，也更新了静态资源对应的 url 地址，现在要发布代码上线，亲爱的前端研发同学，你来告诉我，咱们是先上线页面，还是先上线静态资源？

![image.png](https://upload-images.jianshu.io/upload_images/11846892-f05ceb812e53f51c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* **先部署页面，再部署资源**：在二者部署的时间间隔内，如果有用户访问页面，就会在新的页面结构中加载旧的资源，并且把这个旧版本的资源当做新版本缓存起来，其结果就是：用户访问到了一个样式错乱的页面，除非手动刷新，否则在资源缓存过期之前，页面会一直执行错误。
* **先部署资源，再部署页面**：在部署时间间隔之内，有旧版本资源本地缓存的用户访问网站，由于请求的页面是旧版本的，资源引用没有改变，浏览器将直接使用本地缓存，这种情况下页面展现正常；但没有本地缓存或者缓存过期的用户访问网站，就会出现旧版本页面加载新版本资源的情况，导致页面执行错误，但当页面完成部署，这部分用户再次访问页面又会恢复正常了。

---
好的，上面一坨分析想说的就是：先部署谁都不成！都会导致部署过程中发生页面错乱的问题。所以，访问量不大的项目，可以让研发同学苦逼一把，等到半夜偷偷上线，先上静态资源，再部署页面，看起来问题少一些。
有些公司超变态，没有这样的“绝对低峰期”，只有“相对低峰期”。So，为了稳定的服务，还得继续追求极致啊！

这个奇葩问题，起源于资源的 覆盖式发布，用 待发布资源 覆盖 已发布资源，就有这种问题。解决它也好办，就是实现 非覆盖式发布。

![image.png](https://upload-images.jianshu.io/upload_images/11846892-3f21dab466192813.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看上图，用文件的摘要信息来对资源文件进行重命名，把摘要信息放到资源文件发布路径中，这样，内容有修改的资源就变成了一个新的文件发布到线上，不会覆盖已有的资源文件。上线过程中，先全量部署静态资源，再灰度部署页面，整个问题就比较完美的解决了。

---
所以，静态资源优化方案，基本上要实现这么几个东西：

配置超长时间的本地缓存 —— 节省带宽，提高性能
采用内容摘要作为缓存更新依据 —— 精确的缓存控制
静态资源 CDN 部署 —— 优化网络请求
更资源发布路径实现非覆盖式发布 —— 平滑升级
总之，前端性能优化绝逼是一个工程问题！
---
## 2. 静态资源部署 CDN
CDN[Content Delivery Network] 内容分发网络 主要功能是在不同的地点缓存内容，通过负载均衡技术，将用户的请求定向到最合适的缓存服务器上去获取内容
![image.png](https://upload-images.jianshu.io/upload_images/11846892-80610fe2d3c9090e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
总结一下 CDN 的工作原理：通过权威 DNS 服务器来实现最优节点的选择，通过缓存来减少源站的压力。

---
CDN 应用场景：

静态网页
图片小文件、博客
大文件下载
软件下载、视频点播或图片存储网站
动态加速
直播网站
应用加速
手机 APP
除却 CDN 自身的优势，在前端工程中，将静态文件放到 CDN 上，可以直观地减小资源包大小，同时加快首屏加载。

若不使用 CDN，则所有的资源都会被打包到 app.js 和 vendor.js 中，页面需要等到这两个包下载完成才可以显示。
若使用 CDN，则可以利用浏览器多线程的优势，同时下载若干静态文件以及剩下的 app.js 和 vendor.js，以此达到加快加载的目的。

---

### 3. 自动化文档生成

`commit message` 作用

*   提供更多的历史信息，方便快速浏览
*   过滤某些 commit（比如文档改动），便于快速查找信息
*   直接从 commit 生成 Change log
*   可读性好，清晰，不必深入看代码即可了解当前 commit 的作用。
*   为 Code Reviewing（代码审查）做准备
*   方便跟踪工程历史
*   提高项目的整体质量，提高个人工程素质

目前，社区有多种 Commit message 的写法规范，我们介绍的工具是 commitizen，它使用的是 Angular 规范[AngularJS Git Commit Message Conventions](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.greljkmo14y0)，这是目前使用最广的写法，并且有对应的工具去生成 change log。

**标准说明**

每次提交， Commit message 都包括 Header, Body 和 Footer 三个部分。

```
<type>(<scope>): <subject>
// 空行
<body>
// 空行
<footer>
```

* * *

**Header** 部分只有一行，包括三个字段：type, scope 和 subject 。
type 用于说明提交的类别，只运行使用下面几种，

*   feat: 新功能
*   fix: 修复 bug
*   docs: 仅仅修改了文档，比如 README, CHANGELOG, CONTRIBUTE 等等
*   style: 格式更新（不影响代码运行的变动）
*   refactor: 重构（既不是新增功能，又不是 bug 修复）
*   test: 添加测试
*   chore: 构建过程或辅助工具的变动
*   perf: 优化相关，比如提升性能、体验
*   revert: 回滚到上一个版本
*   ci：自动化流程配置修改

如果是 feat 和 fix ，则这个 commit 将肯定出现在 change log 中，其它情况可自行决定是否放入。

scope 用于说明 commit 影响的范围 比如数据层、控制层、视图层等等，视项目不同而不同。

subject 是 commit 目的的简短描述，不超过 50 个字符。

*   以动词开头，使用第一人称现在时，比如 change，而不是 changed 或 changes
*   第一个字母小写
*   结尾不要加句号

* * *

**Body** 部分是对本次 commit 的详细描述，可分成多行。但是一般我都不写。

**Footer** 部分只用于两种情况。

不兼容变动

如果当前代码与上一个版本不兼容，则 Footer 部分以 BREAKING CHANGE 开头，后面是对变动的描述、以及变动理由和迁移方法。

```
BREAKING CHANGE: isolate scope bindings definition has changed.

    To migrate the code follow the example below:

    Before:

    scope: {
      myAttr: 'attribute',
    }

    After:

    scope: {
      myAttr: '@',
    }

    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
```

* * *

关闭 Issue
如果当前 commit 针对某个 issue ，那么可以在 Footer 部分关闭这个 issue 。

```
Closes #1234
```

```
Closes #1234, $1235, #1236
```

**Revert**

如果当前 commit 用于撤销以前的 commit，则必须以 revert:开头，后面跟着被撤销 Commit 的 Header

```
revert: feat(pencil): add 'graphiteWidth' option
<br />
This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```

*   Body 部分的格式是固定的，必须写成 This reverts commit .，其中的 hash 是被撤销 commit 的 SHA 标识符。
*   如果当前 commit 与被撤销的 commit，在同一个发布（release）里面，那么它们都不会出现在 Change log 里面。如果两者在不同的发布，那么当前 commit，会出现在 Change log 的 Reverts 小标题下面。

* * *

##### 自动生成 changelog 文档

###### commitizen

Commitizen 是一个撰写合格 Commit message 的工具。。

```
npm install -g commitizen
# 在项目目录里，运行下面的命令，使其支持 Angular 的 Commit message 格式。
commitizen init cz-conventional-changelog --save --save-exact
```

用 git cz -m 代替 git commit -m 就可以轻松的写出 Angular 规范的 commit message 了。

###### validate-commit-msg

用于检查 Node 项目的 Commit message 是否符合格式。

###### conventional-changelog

生成 Change log 的工具，运行下面的命令即可。

```
npm install -g conventional-changelog-cli
cd my-project
conventional-changelog -p angular -i CHANGELOG.md -s
```

* * *

如果你的所有 Commit 都符合 Angular 格式，那么发布新版本时， Change log 就可以用脚本自动生成

生成的文档包括以下三个部分。

*   New features
*   Bug fixes
*   Breaking changes.

每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容。

为了方便使用，可以将其写入 package.json 的 scripts 字段。

```
{
  "scripts": {
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -w -r 0"
  }
}
```

```
npm run changelog
```

### 4. 前端埋点
**目的** 
获取用户基本信息、行为以及跟踪产品在用户端的使用情况，并以监控数据为基础，指明产品优化的方向。
**前端监控类别**
前端监控可以分为三类：数据监控、性能监控和异常监控。
`数据监控`就是监听用户信息和行为，常见的监控项有

*PV(page view 页面访问量)：即页面浏览量或点击量
* UV(unique visitor 独立访客)：指访问某个站点或点击某条新闻的不同 IP 地址的人数
* 用户在每一个页面的停留时间
* 用户通过什么入口来访问该网页
* 用户在相应的页面中触发的行为
统计这些数据是有意义的，比如我们知道了用户来源的渠道，可以促进产品的推广，知道用户在每一个页面停留的时间，可以针对停留较长的页面，增加广告推送等等。

`性能监控`指的是监听前端的性能，主要包括监听网页或者说产品在用户端的体验。常见的性能监控项包括：

* 不同用户，不同机型和不同系统下的首屏加载时间
* http 等请求的响应时间
* 静态资源整体下载时间
* 页面渲染时间
* 页面交互动画完成时间
这些性能监控的结果，可以展示前端性能的好坏，根据性能监测的结果可以进一步的去优化前端性能，比如兼容低版本浏览器的动画效果，加快首屏加载等等。

`异常监控`由于产品的前端代码在执行过程中也会发生异常，因此需要引入异常监控。及时的上报异常情况，可以避免线上故障的发上。虽然大部分异常可以通过 try catch 的方式捕获，但是比如内存泄漏以及其他偶现的异常难以捕获。常见的需要监控的异常包括：

* Javascript 的异常监控
* 样式丢失的异常监控
* 服务器请求的异常监控
我们说完了前端监控的三个分类，现在就来聊聊怎么实现前端监控。实现前端监控，第一步肯定是将我们要监控的事项（数据）给收集起来，再提交给后台，最后进行数据分析。数据收集的丰富性和准确性会直接影响到我们做前端监控的质量，因为我们会以此为基础，为产品的未来发展指引方向。
***
#####前端埋点分类


收集监控数据我们是通过前端埋点来实现的，目前常见的前端埋点方法有三种：`  `手动埋点`、`可视化埋点`和`无埋点`。
**手动埋点**，也叫代码埋点，即纯手动写代码，调用埋点 SDK 的函数，在需要埋点的业务逻辑功能位置调用接口，上报埋点数据，像友盟、百度统计等第三方数据统计服务商大都采用这种方案。
优势:

  * 可自定义属性，自定义事件
   * 可以细化需求
   * 相比其他埋点方式减少服务器压力

缺陷:

   * 工程量大的话，手动埋点会出现疏漏，不方便审查。
   * 需求变更要重新埋点，成本高。
   * 每次需求变更都要重新发布版本，对线上系统稳定性有一定危害

```
$(document).ready(() => {
  // ... 这里是你的业务逻辑代码
  sendData(params) //这里是发送你的埋点数据，params是你封装的埋点数据
})
```
***
**前端框架式手动埋点**

如果使用 Vue 或者 React 等前端框架，这些框架都有自己的各种生命周期，为了减少重复性的手动埋点次数，可以在各个生命周期位置，根据你的需求封装你所需的埋点。比如你是 SPA 单页应用，你希望在每一个页面的 componentDidMount 埋点，并由此确定用户已经打开了页面。

css 埋点：
```
.link:active::after {
  content: url('http://www.example.com?action=yourdata');
}
<a class="link">点击我，会发埋点数据</a>
```
***
**可视化埋点**解决了纯手动埋点的开发成本和更新成本，通过可视化工具快速配置采集节点（圈点），在前端自动解析配置，并根据配置上传埋点数据，比起手动埋点看起来更无痕，

比如国外比较早做可视化的是 Mixpanel，国内较早支持可视化埋点的有 TalkingData、诸葛 IO，2017 年腾讯的 MTA 也宣布支持可视化埋点；
***
**无埋点**则是前端自动采集全部事件，上报埋点数据，由后端来过滤和计算出有用的数据。

优点:
前端只要一次加载埋点脚本

缺点:
服务器性能压力山大

采用无埋点技术的有主流的 GrowingIO、神策。无埋点则是前端自动采集全部事件，上报埋点数据，由后端来过滤和计算出有用的数据。

#VI. 总结
---

在业界内有这么一句话：任何简单机械的重复劳动都应该让机器去完成。现代前端技术不再是以前刀耕火种的年代了。所以前端工程化的很多脏活累活都应该交给自动化工具来完成。

如何选型技术、如何定制规范、如何分治系统、如何优化性能、如何加载资源，当你从切图开始转变为思考这些问题的时候，我想说：

你好，工程师！



参考链接：
1.  [前端工程化的理解](https://www.jianshu.com/p/88ed70476adb)
2.  [大公司里怎样开发和部署前端代码？]([https://www.zhihu.com/question/20790576/answer/32602154])
3. [前端埋点的那些事](http://www.imooc.com/article/27151)
4.  [小谈前端埋点](https://www.jianshu.com/p/645a26619508)
2.  [前端工程——基础篇](https://github.com/fouber/blog/issues/10)
3.  [http-proxy-middleware](https://www.jianshu.com/p/a248b146c55a)
4.  [Webpack dev server 使用 http-proxy 解决跨域问题](https://www.jianshu.com/p/3bdff821f859)
5.  [chimurai/http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware)
6.  [详解 webpack-dev-server 的简单使用](https://www.jb51.net/article/137608.htm)
7.  [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin)
8.  [webpack 使用 HtmlWebpackPlugin 进行 cdn 配置](https://www.jianshu.com/p/9248db0349fb)
9.  [CDN 是什么？使用 CDN 有什么优势？](https://blog.csdn.net/const_ly/article/details/79788728)
10.  [webpack4-06-开发、生产环境、动态 CDN 配置](https://www.codercto.com/a/76835.html)
11.  [HTTP 强缓存和协商缓存](https://segmentfault.com/a/1190000008956069)
12.  [Cache-Control 之 no-cache 和 max-age=0](https://www.jianshu.com/p/1744780ddda0)
13.  [Commit message 代码提交规范](https://segmentfault.com/a/1190000019579621)
14.  [Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
15.  [git commit 、CHANGELOG 和版本发布的标准自动化](https://www.cnblogs.com/zivxiaowei/p/10089201.html)

