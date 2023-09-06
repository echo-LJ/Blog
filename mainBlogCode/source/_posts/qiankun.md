---
title: 乾坤-微前端
date: 2023-09-04 15:16:16
tags: qiankun
---

<meta name="referrer" content="no-referrer"/>

## 微前端
* 微前端不是特指某一项技术，而是一种思想。是由2016年 [ThoughtWorks Technology Radar](https://www.thoughtworks.com/radar/techniques/micro-frontends) 中提出的，借鉴后端微服务的架构模式，将 Web 应用由单一的单体应用转变为多个小型前端应用，聚合为一的应用。
* 所以微前端不是指具体的库，不是指具体的框架，不是指具体的工具，而是一种理想与架构模式。
* 微前端的核心三大原则就是：**`独立运行`**、**`独立部署`**、**`独立开发`** 所以满足这些的最佳人选就是 “iframe”!!!

### 微前端能解决什么问题 ?

举例: 一个持续多年的应用，经历几年的业务的更新迭代，当项目发展到一定程度的时候就会遇到以下问题

* 业务模块之间不断的堆叠，交错引用，业务耦合如何治理？
* 老技术、老代码不敢动，新技术、新架构又想用？
* 万年技术债？既要跟随业务敏捷迭代，又要保证代码库向好发展，旧的框架类库如何平稳升级？
* 一个项目多个团队开发，你冲突我，我冲突你，如何解决并行开发的冲突？
* 代码库持续膨胀，难以维护的项目代码，是屎上雕花？还是从头再来？


有没有一种可以分解复杂度，提升协作效率，支持灵活扩展的架构模式？ **`微前端应运而生—— “更友好的iframe” `** 将一个巨无霸应用拆解为一个个独立的微应用应用，而用户又是无感知的！

![截屏2023-09-04 下午4.46.49.png](https://upload-images.jianshu.io/upload_images/11846892-b0ba1a37012f44d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 微前端核心原则：

* `技术栈无关`： 主应用不限制子应用接入的技术栈，每个应用的技术栈选型可以配合业务情景选择。
* `独立开发、独立部署`：既可以组合运行，也可以单独运行。
* `环境隔离`: 应用之间 JavaScript、CSS 隔离避免互相影响.
* `消息通信`：统一的通信方式，降低使用通信的成本.
* `依赖复用`：解决依赖、公共逻辑需要重复维护的问题.

这意味着我们可以循序渐进的进行巨石应用的拆解，去技术升级、去架构尝试、去业务拆解等等。以低成本、低风险的进行，`为项目带来更多可能性`.

### 我们的项目适不适合改造成微前端项目模式？

* 是否有明确的业务边界，业务是否高度集中。
* 业务是否高度耦合、项目是否足够庞大到需要拆分。
* 团队中存在多个技术栈并且无法统一，需要接入同一套主系统。
* 技术老旧，扩展困难，维护吃力不讨好。
* 开发协同、部署维护等工作，效率低下，一着不慎，满盘皆输。

### 微前端技术选型

|  技术方案   | 描述  |  技术栈   |  优点   | 缺点  |  单独构建 / 部署   | 构建速度  |  SPA 体验   | 项目侵入性  |  学习成本   | 通信难度  |  
|  ----  | ----  |  ----  | ----  |  ----  | ----  |  ----  | ----  |  ----  | ----  |  ----  |
| iframe  | 每个微应用独立开发部署，通过 iframe的方式将这些应用嵌入到父应用系统中 | 无限制 | 1. 技术栈无关，子应用独立构建部署 2. 实现简单，子应用之间自带沙箱，天然隔离，互不影响  | 体验差、路由无法记忆、页面适配困难、无法监控、依赖无法复用，兼容性等都具有局限性，资源开销巨大，通信困难 | 支持  | 正常 | 不支持  | 高 | 低  | 高 | 
| Nginx 路由转发  | 通过Nginx配置实现不同路径映射到不同应用 | 无限制  | 简单、快速、易配置 | 在切换应用时触发发页面刷新，通信不易  | 支持 | 正常  | 不支持 | 正常  | 低 | 高  |
| Npm 集成  | 将微应用抽离成包的方式，发布Npm中，由父应用依赖的方式使用，构建时候集成进项目中 |  无限制 | 1. 编译阶段的应用，在项目运行阶段无需加载，体验流畅 2.开发与接入成本低，容易理解  | 1. 影响主应用编译速度和打包后的体积 2. 不支持动态下发，npm包更新后，需要重新更新包，主应用需要重新发布部署 | 不支持  | 慢 | 支持  | 高 | 高  | 正常 |
| 通用中心路由基座式  | 微应用可以使用不同技术栈；微应用之间完全独立，互不依赖。统一由基座工程进行管理，按照DOM节点的注册、挂载、卸载来完成。 | 无限制  | 子应用独立构建，用户体验好，可控性强，适应快速迭代 | 学习与实现的成本比较高，需要额外处理依赖复用  | 支持 | 正常  | 支持 | 高  | 高 | 正常  |
| 特定中心路由基座式 | 微应用业务线之间使用相同技术栈；基座工程和微应用可以单独开发单独部署；微应用有能力复用基座工程的公共基建。 | 统一技术栈  | 子应用独立构建，用户体验好，可控性强，适应快速迭代 | 学习与实现的成本比较高，需要额外处理依赖复用  | 支持 | 正常  | 高 | 高  | 正常 |
| webpack5 模块联邦  | webpack5 模块联邦 去中心模式、脱离基座模式。每个应用是单独部署在各自的服务器，每个应用都可以引用其他应用，也能被其他应用所引用 | 统一技术栈  | 基于webpack5，无需引入新框架，学习成本低，像引入第三方库一样方便，各个应用的资源都可以相互共享应用间松耦合，各应用平行的关系 | 需要升级Webpack5技术栈必须保持一致改造旧项目难度大  | 支持 | 正常  | 低 | 低  |正常 | 

对于选择困难同学来说，可以参考以下纬度进行方案技术的选型

|  参考纬度   | 是否能支持未来的迭代  |
|  ----  | ----  |
|  稳定性  | 该方案是否经历了社区的考验，有较多的成熟案例，同时保持较高的 活跃性  |
|  可拓展性  | 支持定制化开发，提供较高的可拓展能力，同时成本可以在接受范围内  |
|  可控性  | 发生问题后，能够在第一时间内进行问题排查，以最快的响应速度来处理问题，修复的方案是否会依赖于外部环境  |

**市面框架对比：**

* [magic-microservices](https://github.com/bytedance/magic-microservices) 一款基于 Web Components 的轻量级的微前端工厂函数。
* [icestark](https://github.com/ice-lab/icestark) 阿里出品，是一个面向大型系统的微前端解决方案
* [single-spa](https://single-spa.js.org/)是一个将多个单页面应用聚合为一个整体应用的JavaScript 微前端框架
* [qiankun](https://qiankun.umijs.org/zh/guide)蚂蚁金服出品，基于 single-spa 在 single-spa 的基础上封装
* [EMP](https://github.com/efoxTeam/emp/wiki)YY出品，基于Webpack5 Module Federation 除了具备微前端的能力外，还实现了跨应用状态共享、跨框架组件调用的能力
* [MicroApp](https://zeroing.jd.com/)京东出品，一款基于WebComponent的思想，轻量、高效、功能强大的微前端框架

综合以上方案对比之后，我们确定采用了 `qiankun` 特定中心路由基座式的开发方案，原因如下：

* 保证技术栈统一 Vue、微应用之间完全独立，互不影响。
* 友好的“微前端方案“，与技术栈无关接入简单、像iframe一样简单
* 改造成本低，对现有工程侵入度、业务线迁移成本也较低。
* 和原有开发模式基本没有不同，开发人员学习成本较低。
* qiankun 的微前端有 3 年使用场景以及 Issue 问题解决积累，社区也比较活跃，在踩坑的路上更容易自救～


### 接入微前端需要明确的事项！

#### 微前端的运行时容器

* qiankun 所帮你解决的这一块实际上是微前端的运行时容器，这是整个微前端工程化里面其中一个环节
* 从这个角度来讲 qiankun 不算是一个完整的微前端解决方案，而是微前端运行时容器的一个完整解决方案，当你用了 qiankun 之后，你几乎能解决所有的微前端运行时容器的问题，但是更多的一些涉及工程和平台的问题，则需要我们去思考与处理。
* 我们的版本管控、配置下发、监控发布，安全检测、等等这些怎么做，都不是 qiankun 作为一个库所能解答的，这些问题得根据具体情况，来选择适合自己的解决方案
* 对于老旧项目的接入，很难做到零成本迁移，在开发的时候要预留足够的踩坑，魔改代码的时间。如果是已经维持几年堆叠的屎山需要做好因为不规范编码，所产生的各种奇怪的兼容性问题，这个时候你甚至会怀疑，“微前端是否真的有必要?"
* 微前端的核心不是多技术共存，而是`分解复杂度，提升协作效率，支持灵活扩展`，能把“一堆复杂的事情”变成“简单的一件事情”，但是也不是无脑使用的，广东话来说“多个香炉多只鬼”，每多一个技术栈都会增加：维护成本，兼容成本，资源开销成本，这些都会无形的拖累生产力。
* 基座应用与微应用之间，强烈推荐使用相同的技术栈，相同的技术栈`可以实现公共依赖库、UI库等抽离，减少资源开销，提升加载速度`，最重要的是：“`减少冲突的最好方式就是统一`”，通过约束技术栈可以尽可能的减少项目之间的冲突，减少工作量与维护成本。

#### 微前端初尝试
* 对于微前端的接入最好的时候就是，刚开始不久或重要性不是特别强的项目，一方面项目具备兼容微前端的工程能力，另一方面项目使用微前端方案的成本最低，不需要改太多代码
* 对于老旧项目的接入建议还是从边缘简单的模版入手，逐步分解

#### 标准化才能提升生产力

*混乱的项目会拖累生产效率，同时混乱的微前端也会加剧内耗，所以只有标准化才能提升生产力。
*解决微前端的接入问题是最简单的，但是微前端接入后的：工程化，应用监控，应用规范，应用管理才是微前端中困难的地方，如果你只是想简单的嵌入一个应用，我推荐你的使用 ”iframe“

#### qiankun 不支持 Vite ！！！

* [未来是否考虑支持 vite](https://github.com/umijs/qiankun/issues/1257)
* 不建议尝试去改变目前的 qiankun，Vite的改造成本真的太高了，虽然webpack 比Vite慢，但是经过拆分的应用内容已经很小了，不会对项目有太大的拖累。


### 微前端拆分原则

**微应用的拆与合思考：拆的是系统复杂度，合的是系统复用度 核心原则：高内聚，低耦合**

微应用的拆解没有具体规则，但是以下规则应该可以给你在进行系统拆分时提供一些依据。

1. `尽量减少彼此的通信和依赖` 微前端的通信交互、链接跳转等操作所带来等成本其实是很大的，所以在拆分的时候尽量“完全独立，互不依赖”.
2. 微应用的拆分的时候切忌“`盲目细致拆分`”，过度拆分会导致 “做的很牛逼，但是没有用的困局”，微应用的拆分并不是一步到位的，我们要根据实际情况逐步拆分。如果一开始不知道应该划分多细，可以先粗粒度划分，然后随着需求的发展，逐步拆分。
    - 如：现在有一个售后管理系统，我们按业务线拆分为：客服管理，库存管理，物流管理，未来客服管理需求功能持续庞大再拆解为：智能客服、电话客服、在线客服。而这些客服，又可以嵌入供应商管理中心，商品管理中心 等项目使用。
3. 在拆分的时候我们应该尽量考虑未来场景：渐变式技术栈迁移，前端应用聚合、多系统业务复用，如何做业务解耦和代码复用。
4. 应用之间应该尽量解耦，子应用的事情就应该由子应用来做。
    - 如：子应用的一些标识，如：路由前缀，应用名称，根节点容器名称，依赖库的使用
    - 需要明确什么是子应用应该维护的，什么是父应用应该维护的，如果什么资源都一股脑的使用父应用下发，则会导致应用之间耦合严重。

#### 建议按照业务域来做拆分

1. 保持核心业务的独立性，把无关的子业务拆分解耦。业务之间开发互不影响，业务之间可拆解微应用，单独打包，单独部署。
2. 业务关联紧密的功能单元应该做成一个微应用、反之关联不紧密的可以考虑拆分成多个微应用，判断业务关联是否紧密的标准：看这个微应用与其他微应用是否有频繁的通信需求。
3. 如果有可能说明这两个微应用本身就是服务于同一个业务场景，合并成一个微应用可能会更合适。
4. 分析平台差异，平台差异大可以根据平台特性拆分
5. 分析页面结构，如果结构清晰，可以根据结构拆分
6. 分析产品业务，将产品逻辑耦合度高的功能合并到一起.

###  引入qiankun - 在主应用中注册微应用

![截屏2023-09-05 下午2.16.07.png](https://upload-images.jianshu.io/upload_images/11846892-9ce972ecc49d470a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 选择基座的模式？

1. `通用中心路由基座式：只有公共功能的主应用` (菜单栏、登录、退出...)不包含任何业务逻辑
2. `特定中心路由基座式： 一个含业务代码的项目` 作为基座，所有新功能作为子应用引入

以下案例是以Vue技术栈作为应用技术栈，建议应用之间还是统一技术栈，降低维护、上手、学习成本。越是不同技术、不同库的版本不同需要做的处理就越更多。

### qiankun 注册微应用的方式：

> 自动模式：使用 registerMicroApps + start，路由变化加载微应用

* 当微应用信息注册完之后，一旦浏览器的 url 发生变化，便会自动触发 qiankun 的匹配
    - 首次load应用，创建子应用实例，渲染。
    - 切到其他子应用后切回，会重新创建新的子应用实例并渲染。
    - 之前的子应用实例 qiankun 直接不要了，即使你没有手动销毁实例。
    - 采用这种模式的话 一定要在子应用暴露的 unmount 钩子里手动销毁实例，不然会导致内存泄漏。
* activeRule - string | (location: Location) => boolean | Array<string | (location: Location) => boolean> 必选，微应用的激活规则。
* 支持直接配置字符串或字符串数组，如 activeRule: '/app1' 或 activeRule: ['/app1', '/app2']，当配置为字符串时会直接跟 url 中的路径部分做前缀匹配，匹配成功表明当前应用会被激活。
* 支持配置一个 active function 函数或一组 active function。函数会传入当前 location 作为参数，函数返回 true 时表明当前微应用会被激活。如 location => location.pathname.startsWith('/app1')

**`自动挂载：registerMicroApps + start`**

```
$ yarn add qiankun // ps：只需要主应用安装即可
```
```
// 主应用/scr/main.js 
import { registerMicroApps, start } from 'qiankun';

// 1. 获取微应用配置
const MICRO_CONFIG = [
  {
    name: 'vue app', // 应用的名字 必填 唯一
    entry: '//localhost:7100', // 默认会加载这个html 解析里面的js 动态的执行 （子应用必须支持跨域）fetch
    container: '#yourContainer', // 挂载具体容器 ID
     // 3. 根据路由匹配，激活的子应用
    activeRule: '/yourActiveRule',
    props: {
        xxxx: '/' // 下发给子应用
    }
  }
]

// 2. 注册微应用
registerMicroApps(MICRO_CONFIG)

start() // 启动微服务


```

**activeRule 规则示例：此处拿官网的举例～ ctiveRule：'/app1'**
**当微应用信息注册完之后，一旦浏览器的 url 发生变化，便会自动触发 qiankun 的匹配逻辑。 所有 activeRule 规则匹配上的微应用就会被插入到指定的 container 中，同时依次调用微应用暴露出的生命周期钩子。**


> 手动模式：使用 loadMicroApp 手动注册微应用

* 每个子应用都有一个唯一的实例ID，reload时会复用之前的实例
* 如果需要卸载则需要手动卸载 `xxxMicroApp.unmount()`

由于registerMicroApps的特性，会导致路由的keep alive 失效，故本文使用 loadMicroAp + router.beforeEach 进行来达到自动注册的目的

* 使用场景：如果微应用不是直接跟路由关联的时候，你可以选择手动加载微应用的方式会更加灵活。
**手动挂载: loadMicroApps**
```
// 任意页面都可以注册

import { loadMicroApp } from 'qiankun';

// 获取应用配置并手动挂载，挂载后返回挂载对象
this.microApp = loadMicroApp({
    name: 'vue app', // 应用的名字 必填 唯一
    entry: '//localhost:7100', // 默认会加载这个html 解析里面的js 动态的执行 （子应用必须支持跨域）fetch
    container: '#yourContainer', // 挂载具体容器 ID
    activeRule: '/yourActiveRule', // 根据路由 激活的子应用
    props: {
        xxxx: '/' // 下发给子应用
    }
})

this.microApp.unmount() // 手动销毁～


```

### 微应用挂载节点

`微应用可以挂载在页面的任意位置，微应用、微项目、微页面、微组件，一切皆有可能。`

* 微应用两种常见的挂载场景: 1、路由页内挂载，把子应用内嵌页入使用

![截屏2023-09-05 下午3.43.36.png](https://upload-images.jianshu.io/upload_images/11846892-d8da1a7178144481.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
// 主应用/src/views/About.vue
<template>
  <div class="about">
    <div id="sub-app-container"></div>
  </div>
</template>
```
* 微应用两种常见的挂载场景: 2、根DOM中与主应用同级挂载，切换的时候隐藏应用，显示当前应用

![截屏2023-09-05 下午3.45.46.png](https://upload-images.jianshu.io/upload_images/11846892-38508e7df6ceb468.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
// 主应用/scr/App.vue
<template>
    <div id="app">
        <!-- 不同的微应用 -->
        <div v-show="location.hash.startsWith('#/operation')" id="sub-operation-container"></div>
        <div v-show="location.hash.startsWith('#/inventory')" id="sub-inventory-container"></div>
    </div>
</template>

```

### 应用加载解析流程图

`简易的图示了qiankun是如何通过 import-html-entry 加载微应用的`

![截屏2023-09-05 下午3.47.07.png](https://upload-images.jianshu.io/upload_images/11846892-3cc9e2b96f8daf3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**简易流程：**

* qiankun 会用 原生`fetc`h方法，请求微应用的 `entry` 获取微应用资源，然后通过 `response.text` 把获取内容转为字符串。
* 将 HTML 字符串传入 `processTpl` 函数，进行 HTML 模板解析，通过正则匹配 HTML 中对应的 javaScript（内联、外联）、css（内联、外联）、代码注释、entry、ignore 收集并替换，去除` html/head/body` 等标签，其他资源保持原样
* 将收集的 styles 外链URL对象通过 `fetch` 获取 css，并将 css 内容以` <style>` 的方式替换到原来 `link`标签的位置
* 收集 `script` 外链对象，对于异步执行的 JavaScript 资源会打上 `async` 标识 ，会使用 `requestIdleCallback` 方法延迟执行。
* 接下来会创建一个匿名自执行函数包裹住获取到的 js 字符串，最后通过 `eval` 去创建一个执行上下文执行 js 代码，通过传入 `proxy` 改变 `window `指向，完成 JavaScript `沙箱隔离。[源码位置](https://github.com/kuitos/import-html-entry/blob/76df4b3737d54112f6bf2dfabcd01709079468e4/src/index.js#L45)。
* 由于 qiankun 是自执行函数执行微应用的 JavaScript，因此在加载后的微应用中是看不到 JavaScript 资源引用的，只有一个资源被执行替换的标识。
当一切准备就绪的时候，执行微应用的 JavaScript 代码，渲染出微应用.


### 微应用接入三步走

#### 微应用的入口文件 修改 webpack_public_path
* 在 src 目录新增 `public-path.js`
* `webpack` 默认的 `publicPath` 为 `""` 空字符串，会基于当前路径来加载资源。但是我们在主应用中加载微应用资源的时候会导致资源丢失，所以需要重新设置 `__webpack_public_path__ `的值.

```
// 微应用/src/const/public-path.js
if (window.__POWERED_BY_QIANKUN__) {
    __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
} 
```
#### 微应用webpack 新增配置
* webpack 配置修改 PS: 什么是 umd 模块？
```
const { name } = require('./package.json')

module.exports = {
  devServer: {
      port: 8081, // 父应用配置微应用端口，要与微应用端口一致
      disableHostCheck: true, // 关闭主机检查，使微应用可以被 fetch
      headers: {
          'Access-Control-Allow-Origin': '*' //因为qiankun内部请求都是fetch来请求资源，所以子应用必须允许跨域
      }
  },
  configureWebpack: {
      output: {
          library: `${name}-[name]`, // 微应用的包名，这里与主应用中注册的微应用名称一致
          libraryTarget: 'umd', // 这里设置为umd意思是在 AMD 或 CommonJS 的 require 之后可访问。
          jsonpFunction: `webpackJsonp_${name}` // webpack用来异步加载chunk的JSONP 函数。
      }
  }
}

```

#### 微应用添加生命周期

微应用需要在自己的入口文件，添加 `bootstrap`、`mount`、`unmount` 三个生命周期钩子，供主应用在适当的时机调用。

* main.js 注册微应用，增加判断让子应用就算脱离了父应用也可以独立运行
* PS：qiankun 生命周期函数都必须是 Promise，使用 async 会返回一个Promise对象

```
// 微应用/scr/main.js

import './public-path.js'
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

let instance = null

// 1. 将注册方法用函数包裹，供后续主应用与独立运行调用
function render(props = {}) {
  const { container } = props
  instance = new Vue({
    router,
    store,
    render: h => h(App),
  }).$mount(container ? container.querySelector('#app-micro') : '#app-micro')
}

// 判断是否在乾坤环境下，非乾坤环境下独立运行
if (!window.__POWERED_BY_QIANKUN__) {
  render()
}

// 2. 导出的生命周期
/**
 * bootstrap 只会在微应用初始化的时候调用一次，下次微应用重新进入时会直接调用 mount 钩子，不会再重复触发 bootstrap。
 * 通常我们可以在这里做一些全局变量的初始化，比如不会在 unmount 阶段被销毁的应用级别的缓存等。
 */
export async function bootstrap() {
  console.log('[vue] vue app bootstraped')
}

/**
 * 应用每次进入都会调用 mount 方法，通常我们在这里触发应用的渲染方法
 */
export async function mount(props) {
  console.log('[vue] props from main framework', props)
  render(props);
}

/**
 * 应用每次 切出/卸载 会调用的方法，通常在这里我们会卸载微应用的应用实例
 */
export async function unmount() {
  instance.$destroy()
  instance.$el.innerHTML = ''
  instance = null
}

/**
 * 可选生命周期钩子，仅使用 loadMicroApp 方式加载微应用时生效
 */
export async function update(props) {
  console.log('update props', props)
}

```

小结: 经历这几步，qiankun 父应用与微应用就接入完成了。当父应用完成加载微应用的时候，微应用就会遵循对应的解析 规则，插入到父应用的HMTL中了。

### 预加载微应用

`预先请求子应用的 HTML、JS、CSS 等静态资源，等切换子应用时，可以直接从缓存中读取这些静态资源，从而加快渲染子应用。`

#### registerMicroApps 模式下在 start 方法配置预加载应用
```
import { registerMicroApps, start } from 'qiankun';

registerMicroApps([...AppsConfig])

start({ prefetch: "all" }) // 配置预加载

```

* prefetch - boolean | 'all' | string[] | (( apps: RegistrableApp[] ) => { criticalAppNames: string[]; minorAppsName: string[] }) - 可选，是否开启预加载，默认为 true。
  - 配置为 true 则会在第一个微应用 mount 完成后开始预加载其他微应用的静态资源
  - 配置为 all 则主应用 start 后即开始预加载所有微应用静态资源
  - 配置为 string[] 则会在第一个微应用 mounted 后开始加载数组内的微应用资源
  - 配置为 function 则可完全自定义应用的资源加载时机 (首屏应用及次屏应用)

#### loadMicroApps 模式下

```
import { prefetchApps } from 'qiankun';

export const MICRO_PREFETCH_APPS = [
    { name: 'vue-child', entry: '//localhost:7101/' },
    { name: 'vue-app', entry: '//localhost:8081/' }
]

prefetchApps(MICRO_PREFETCH_APPS)

```

笔者用的模式就是 loadMicroApps 模式，为了日后维护的便携性，改造一下：，新增 isPreload 字段维护是否开启预加载，这样有关于微应用的信息都在此 js文件维护，避免散弹式修改。

```
// 基座/src/const/micro/application-list.js

export const MICRO_CONFIG =  [
    {
        name: 'you app name', // 应用的名字
        entry: '//localhost:7286/', // 默认会加载这个html 解析里面的js 动态的执行 （子应用必须支持跨域）fetch
        container: '#yuo-container-container', // 容器id
        activeRule: '/your-prefix', // 根据路由激活的路径
        isPreload: true, // !! 是否开启预加载 !!
    }
]

```
```
import { prefetchApps } from 'qiankun';
import { MICRO_CONFIG } from '@/const/micro/application-list.js';

// 获取配置的 isPreload 字段，并生成加载对应的格式
const MICRO_PREFETCH_APPS = MICRO_CONFIG.reduce(
    (total, { isPreload, name, entry }) => (isPreload ? [...total, { name, entry }] : total),
    []
)
// 预加载应用
prefetchApps(MICRO_PREFETCH_APPS)

```

### 路由模式选择与改造

`最好的路由模式就是主应用、子应用都统一模式，可以减少不同模式之间的兼容工作`

本文选择统一为： **父子路由hash 模式**

|  主模式   | 子模式  | 推荐  | 接入影响  | 解决方案  | 改造成本  |
|  ----  | ----  | ----  | ----  | ----  | ----  |
| hash  | hash | 强烈推荐  | 无  |   |  |
| hash  | history | 不推荐  | 有  | history.pushState  |改造成本大  |
| history  | history | 强烈推荐  | 无  |   |  |
| history  | hash | 推荐  | 无  |   |   |     |  

⚠️： PS: 每个模式之间的组合并不是接入就可以完成的，都需要一些改造，如：增加路由前缀，路由配置base设置，不同的模式activeRule的规则都不同。

#### 路由改造工作

##### 新增微应用路由前缀
`新增前缀不是微应用必须的，但是为了从 URL 上与其他应用隔离，也是为了接入旧应用的时候，能让 activeRule 方法能识别并激活应用，故新增路由前缀。`

**`父应用路由表`**
```
[
    // 主应用 router.js：如果想匹配任意路径，我们可以使用通配符 (*)：
    {
        path: '/your-prefix',
        name: 'Home',
        component: Home
    },
     // 特定页面兜底 会匹配以 `/your-prefix` 开头的任意路径
     // 如：/your-prefix/404 ， /your-prefix/no-permission ....
    {
        path: '/your-prefix/*',
        name: 'Home',
        component: Home
    }
]

```
⚠️：子应用路由切换，由于应用与路由都是通过 URL 注册与销毁的，当子应用路由跳转地址，无法与父应用的路由地址匹配上的时候页面会销毁，需要注意路由匹配，或者增加路由兜底。

**`子应用 hash 模式`**
```
// hash 模式不能使用base，只能改前缀
new VueRouter({
    mode: 'hash',
    routes: [
        {
            //增加路由前缀判断
            path: `${ window.__POWERED_BY_QIANKUN__ ? 'your-prefix' : ''}/login`,
            component: _import('login/index.vue')
        }
    ]
})

```

**`子应用 history 模式`**
```
new VueRouter({
    mode: 'history',
    // **针对子应用是 history模式的时候，只用设置 router base 就好了，不用像hash 这么麻烦** 
    base: window.__POWERED_BY_QIANKUN__ ? 'your-prefix' : null,
    routes: [
        {
            path: '/login',
            component: _import('login/index.vue')
        }
    ]
})

```

###  旧项目路由接入改造

`但是由于笔者是接入的是旧项目并且又是 hash 路由模式想顺利接入，一个个加三元则改动太多路由表了，为了减少对于旧项目接入时的影响仅在以下三处做修改（ps：因为懒）`

#### hash路由模式：格式化路由表对象，微路由表路径，别名，重定向增加前缀区分应用

* 这里我们利用递归函数需要给路由动态增加前缀、path 、redirect、alias 这个三种状态需要动态处理一下

**`路由表数据`**
```
const routes = [
    {
        path: '/home',
        name: 'home',
        component: Home
    },
    {
        path: '/about',
        name: 'about',
        redirect: 'home',
        component: () => import('../views/About.vue')
    },
		{
        path: '/about',
        name: 'about',
        alias: '/user/about',
        component: () => import('../views/About.vue')
    }
]

```
**`格式化路由方法`**
```
let SUN_ROUTER_PATH_LIST = [] // 所有被格式化的路由都记录一下

// 1. 格式化路由器参数 递归格式化
export function formatRouterParams(parameter) {
    // 判断如果不是 qiankun 环境内容则原样返回
    if (!window.__POWERED_BY_QIANKUN__) {
        return parameter.data
    }
    
    // 递归函数：data：数据源，params：需要替换的参数（数组、字符串），value需要替换的值（函数），deepKey：判断是否需要递归的参数名
    const recursionData = ({ data, params, value, deepKey }) => {
        return data.reduce((total, item) => {
            item = formatData({ item, params, value })
            // 判断是否需要继续递归
            if (deepKey && item[deepKey] && Array.isArray(item[deepKey])) {
                item[deepKey] = recursionData({
                    data: item[deepKey],
                    params,
                    value,
                    deepKey
                })
            }
            return [...total, item]
        }, [])
    }
    return recursionData(parameter)
}

// 2. 格式化路由数据增加前缀
export function formatData({ item, params, value }) {
    if (!item) return
    
    // 如果params是数组：[path, ...more] 遍历增加前缀
    if (Array.isArray(params)) {
        params.forEach(key => {
            if (Object.prototype.hasOwnProperty.call(item, key)) {
                item[key] = geRouterValue(value, item[key])
            }
        })
    } else if (params) {
        item[params] = geRouterValue(value, item[params])
    }

    // 记录格式化路由路径
    SUN_ROUTER_PATH_LIST.push(item)
    return item
}

// 3. 判断value是函数还是值，如果是函数则调用函数 返回对应的value 值
export function geRouterValue(value, key) {
    return typeof value === 'function' ? value(key) : value
}

```

**`调用递归方法统一替换微应用路由表`**
```
const BASE_ROUTER_PATH = 'your-prefix'

const router = new VueRouter({
    mode: 'hash',
    // 调用方法格式化路由表参数！！！
    routes: formatRouterParams({
        data: route,
        deepKey: 'children',
        params: ['path', 'redirect', 'alias'],
        value: value => { // value 格式化的方法
            if (window.__POWERED_BY_QIANKUN__ && typeof value === 'string') {
                 const path = value[0] === '/' ? value : `/${value}`
                 return BASE_ROUTER_PATH + path
            }
            return value
        }
    })
})

```

**`遍历结果返回-路由表，统一增加“your-prefix”前缀啦`**

```
[
    {
        "path": "your-prefix/home",
        "name": "home",
        "component": "Home"
    },
    {
        "path": "your-prefix/about",
        "name": "about",
        "redirect": "your-prefix/home",
        "component": ""
    },
    {
        "path": "your-prefix/about",
        "name": "about",
        "alias": "your-prefix/user/about",
        "component": ""
    }
]
```

#### router.beforeEach

`跳转的时候调用检查跳转函数，判断是否需要增加前缀`

```
// 路由全局守卫 
// ps: 在内部判断，如果跳转的路由与当前子应用无关，直接放行即可
router.beforeEach((to, from, next) => {
    checkLink(to, next, () => {
        next()
    })
})

```
* 简单来说： 如果是在 qiankun 环境中，并且不是跳转其他微应用的path， 并且跳转不是格式化前缀的路径，并且当前拼接的地址与格式化的路由地址是一致的才拼接 `next`.
```
// LINK_MICRO_APP_LIST, SUN_ROUTER_PATH_LIST 变量上文有记录

const { name } = require('../../package.json')
export const BASE_ROUTER_PATH = `/${name}` // 此处笔者用了 package.json 的 name 做为 “your-prefix” 方便后期维护

// 跳转的时候检查，判断是否需要增加前缀
export function checkLink(to, next, callback) {
    // 是否存在qinakun环境中
    const IS_HAVE_QIANKUN = window.__POWERED_BY_QIANKUN__
    // 是否跳转其他微应用
    const IS_JUMP_TO_MICRO_APP = Object.values(LINK_MICRO_APP_LIST).includes(to.path)
    // 是否跳转的是根路径
    const IS_BASE_PATH_SYMBOL = to.path === '/'
    // 根路径是否匹配一致
    const IS_HAVE_BASE_ROUTER_PATH = getBasePath(to.path, '/') === getBasePath(BASE_ROUTER_PATH, '/')
    // 判断以上情况来确定是否需要给路由动态增加前缀
    const IS_ADD_PREFIX = IS_HAVE_QIANKUN && !IS_JUMP_TO_MICRO_APP && !IS_HAVE_BASE_ROUTER_PATH
    if (IS_ADD_PREFIX || IS_BASE_PATH_SYMBOL) {
        const path = `${BASE_ROUTER_PATH}${to.path}`
        // 当前拼接的地址与 当前格式化的路由是否一致，一致才是跳转内部路由
        if (SUN_ROUTER_PATH_LIST.some(e => [e.path, e.redirect, e.alias].includes(path))) {
            next({ path })
        }
    }
    // 执行回调函数
    callback && callback()
}

// 获取当前的基础路径  如: getBasePath('/user/age/xxx', '/') => '/user'
export function getBasePath(path, prefix = '') {
    if (!path) return
    const pathArray = String(path).split('/').filter(item => item)
    const basePath = prefix + pathArray[0]
    return basePath
}

```

#### 改写router.push OR router.replace
```
// 因为改变了next()的地址, push方法会提示报错，这里过滤一下～
const originalPush = VueRouter.prototype.push
VueRouter.prototype.push = function push(location) {
    return originalPush.call(this, location).catch(err => err)
}
```

注意：ps: 其可以直接在push中改写， 省略 router.befroeEach

```
const originalPush = VueRouter.prototype.push

VueRouter.prototype.push = function push(location) {
    const IS_JUMP_TO_MICRO_APP = Object.values(LINK_MICRO_APP_LIST).includes(location.path)
    const IS_HAVE_QIANKUN = window.__POWERED_BY_QIANKUN__
    const IS_BASE_PATH_SYMBOL = location.path === '/'
    const IS_HAVE_BASE_ROUTER_PATH = getBasePath(location.path, '/') === BASE_ROUTER_PATH
    const IS_ADD_PREFIX = IS_HAVE_QIANKUN && !IS_JUMP_TO_MICRO_APP && !IS_BASE_PATH_SYMBOL && !IS_HAVE_BASE_ROUTER_PATH

    if (IS_ADD_PREFIX) {
        location.path = `${BASE_ROUTER_PATH}${location.path}`
    }
    return originalPush.call(this, location)
}

```

#### 路由跳转记录


**`跳转方式: 路由跳转与正常使用无异`**
* ps: 在父应用是history模式，子应用是 hash模式的时候 子应用需要特殊处理一下URL重定向切换 history.pushState

**`跳转其他微应用`**

* 由于我们的应用之间式分离的，所以跳转外部应用的路由也是分离的，如果在项目中字面量固定写死风险太大了，如果外部应用发生一点改变，需要改项目里的路径的时候将会是一个噩梦，所以我们统一使用在各自的微应用维护一个常量列表去处理记录应用之间的跳转，方便全局统一管理。此处仅笔者一点拙见，如有更好建议请多多发表。
* PS：此处跳转的常量列表其实也可以放到基座应用去维护，但是最佳选择是有运维平台去维护应用之间跳转关系会更好～

```
// 微应用/scr/const/link-micro-app-list
export const LINK_MICRO_APP_LIST = {
    CHILD_VUE: '/child/vue', // vue 微应用地址
    CHILD_REACT: '/child/react', // react 微应用地址
    USER_INFO: '/user/info' // 跳转父应用用户信息页地址
}
```
**`使用场景：`**

```
// 微应用/router.js

// 1. 判断是否跳转其他应用
const IS_JUMP_TO_MICRO_APP = Object.values(LINK_MICRO_APP_LIST).includes(to.path)

// 2. 定义路由的时候通过路由列表地址获取
let routes = [
    {
        path: '/about',
        name: 'about',
        // 别名 重定向 凡是跳转其他应用的都应该在这里统一管理 ！！！
        redirect: LINK_MICRO_APP_LIST['CHILD_VUE'], 
        component: () => import('../views/About.vue')
    }
]

// router.beforeEach
router.beforeEach((to, from, next) => {
    if (IS_JUMP_TO_MICRO_APP) {
        next(false) // 禁止跳转其他ying yon
    }
  // ... more code
})

```

### 微应用与路由之间 如何 keep alive

#### registerMicroApps模式下，为什么切换路由会导致应用重载？
* 详情可以看上文 “引入qiankun - 在主应用中注册微应用”
* URL 改变时应用匹配切换，路由的切换会导致应用的卸载与加载
    - 例：A 到 B， 触发A unmount ⇒ 判断 B 是否加载过，已加载则触发 mount，未加载则触发 bootstrap ⇒ mount
* 如果子应用挂载在内部路由，路由跳转也将触发应用的重载
* 应用切换导致重载，导致组件状态丢失，为了保持应用实例不被加载，我们需要手动的控制应用的注册与销毁

#### 方案一：loadMicroApp

* 优点：在一个页面中可以同时挂载多个微应用
* 缺点：无法根据路由匹配规则来挂载应用
* 适用场景：当需要在一个页面中同时挂载2个以上子应用，并且子应用的挂载不需要通过路由匹配来实现。
* PS：在基座中关闭标签页时，需要手动调用app的unmount钩子销毁应用，不然再次新建页签进入时还是以前的实例


loadMicroApp不能根据路由规则来挂载应用不是qiankun的问题，是我们的问题～

**`使用 router.afterEach + loadMicroApp 的解决应用 keep alive，思路是通过判断路由守卫的地址，如果是符合激活规则的则激活应用`**

*  主应用 router 路由守卫
```
// 主应用/src/router/index.js
// 1. 全局后置钩子调用微应用加载方法
// 为什么笔者会在这里调用呢，其实是笔者利用了JavaScript机制的宏任务，目的就是为了在路由页获取是微应用的容器
// 是否挂载了微应用，因为有时候微应用会因为作者系统的路由切换而被替换掉，所以用这个方式解决
router.afterEach(to => {
    setTimeout(() => { // setTimeout 是宏任务的一种
        microApplicationLoading(to.path) // 把当前跳转的路径传入
    })
})


```
* 判断微应用加载的方法 `microApplicationLoading`

应用表
```
// **src/const/micro/application-list.js** 
export const microApplicationList [
    {
        name: 'you app name', // 应用的名字
        entry: '//localhost:7286/', // 默认会加载这个html 解析里面的js 动态的执行 （子应用必须支持跨域）fetch
        container: '#yuo-container-container', // 容器id
        activeRule: '/your-prefix', // 根据路由激活的路径
        **isPreload: true, // !! 是否开启预加载 !!
        isRouteStart: true, // 是否需要路由启动**
        props: { // 下发子应用的资源
            router: router,
            store: store,
            parentEventHub: parentEventHub
        }
    }
]

```

加载微应用方法

```
// 主应用/src/const/micro/qianun-utils.js
// 加载微应用方法
import { loadMicroApp } from 'qiankun'

export async function microApplicationLoading(path) {
    // 1. 根据路由地址加载当前应用配置
    let currentActiveMicroConfig = await store.dispatch('d2admin/micro/GET_FIND_MICRO_CONFIG', path)
		
    // 2. 从 vuex 获取缓存的微应用列表
    const microApplicationList = store.getters['d2admin/micro/microApplicationList']

    // 3. 如果没有匹配应用配置则代表跳转的不是微应用 or 微应用配置不需要路由启动的属性
    if (!currentActiveMicroConfig || !currentActiveMicroConfig.isRouteStart) {
        return
    }

    // 4. 根据应用配置 获取缓存的应用
    const cacheMicro = microApplicationList.get(currentActiveMicroConfig.activeRule)
	
    // 5. 判断当前挂载的是否有内容
    const containerNode = getContainerNode(currentActiveMicroConfig.container)
    const isNoTNodeContents = containerNode !== -1 && !containerNode
	
    // 6. 如果没有dom节点 or 没有缓存应用配置 注册一下
    if (isNoTNodeContents || !cacheMicro) {
			
        // 如果有缓存应用配置，但是容器没有应用挂载，先卸载缓存应用再注册微应用
        if (cacheMicro) {
            cacheMicro.unmount()
            cacheMicro.unmountPromise.then(() => {
                loadRouterMicroApp(currentActiveMicroConfig)
            })
            return
        }

        // 加载应用
        loadRouterMicroApp(currentActiveMicroConfig)
    }
}

// 加载微应用
export function loadRouterMicroApp(currentApp) {
    const micro = loadMicroApp(currentApp)
    micro.mountPromise.then(() => {
        // 挂载完成 设置一下vuex微应用列表
        store.dispatch('d2admin/micro/SET_MICRO_APPLICATION_LIST', {
            key: currentApp.activeRule,
            value: micro
        })
    })
}

// 获取容器节点
export function getContainerNode(container) {
    const containerNode = container && document.querySelector(container)
    
    if (containerNode) {
        return containerNode.childNodes.length
    }
    
    return -1
}

```

vuex 方法 记录一下注册应用对象

```
// 主应用/src/store/modules/d2admin/modules/micro.js
import MICRO_CONFIG from '@/const/micro/application-list.js '

export default {
    state: {
        microApplicationList: new Map([]) // 已经注册的微应用列表
    },
    getters: {
        microApplicationList(state) {
            return state.microApplicationList
        }
    },
    actions: {
        // 设置微应用程序列表
        SET_MICRO_APPLICATION_LIST({ state, dispatch }, { key, value }) {
            state.microApplicationList.set(key, value)
        },
        // 通过路径获取微应用配置
        GET_FIND_MICRO_CONFIG({ state }, path) {
            return MICRO_CONFIG.find(e => {
                return getPathPrefix(path, '/') === getPathPrefix(e.activeRule, '/')
            })
        }
    }
}

// 获取当前的基础路径  如: getPathPrefix('/user/age/xxx', '/') => '/user'
export function getPathPrefix(path, prefix = '') {
    if (!path) return
    const pathArray = String(path).split('/').filter(item => item)
    const basePath = prefix + pathArray[0]
    return basePath
}

```

[ 更多的 keep-alive 解决方案](https://juejin.cn/post/6856569463950639117#heading-11)

### 沙箱模式

#### CSS沙箱

微前端对于样式隔离问题，目前相关配套还不是很成熟

![截屏2023-09-05 下午5.29.06.png](https://upload-images.jianshu.io/upload_images/11846892-a820b5e1c5c1ae8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


---

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [万字长文-落地微前端 qiankun 理论与实践指北](https://juejin.cn/post/7069566144750813197)
