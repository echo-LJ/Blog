---
title: 基于 qiankun 的微前端最佳实践
date: 2021-05-28 13:42:34
tags: 微前端
---

<meta name="referrer" content="no-referrer"/>

## 一、 介绍
**`什么是微前端`**
Techniques, strategies and recipes for building a modern web app with multiple teams that can ship features independently. [-- Micro Frontends](https://micro-frontends.org/)

微前端是一种多个团队通过独立发布功能的方式来共同构建现代化 web 应用的技术手段及方法策略。

## 二、 引言
* 新本文是基于 qiankun 的微前端最佳实践文章，本文将分享如何使用 qiankun 如何搭建主应用基座，然后接入不同技术栈的微应用，完成微前端架构的从 0 到 1。

* 本教程采用 Vue 作为主应用基座，接入不同技术栈的微应用。如果你不懂 Vue 也没关系，我们在搭建主应用基座的教程尽量不涉及 Vue 的 API，涉及到 API 的地方都会给出解释。

`注意：qiankun 属于无侵入性的微前端框架，对主应用基座和微应用的技术栈都没有要求。`
我们在本教程中，接入了多技术栈 微应用 的 主应用 最终效果图如下：

缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图

## 三、 构建主应用基座
---
<!-- 我们以 实战案例 - feature-inject-sub-apps 分支 （案例是以 Vue 为基座的主应用，接入多个微应用） 为例，来介绍一下如何在 qiankun 中如何接入不同技术栈的微应用。 -->
案例是以 Vue 为基座的主应用，接入多个微应用 为例，来介绍一下如何在 qiankun 中如何接入不同技术栈的微应用。

我们先使用 vue-cli 生成一个 Vue 的项目，初始化主应用。
```
$ vue create micro-app-main
```
将普通的项目改造成 qiankun 主应用基座，需要进行三步操作：

* 创建微应用容器 - 用于承载微应用，渲染显示微应用；
* 注册微应用 - 设置微应用激活条件，微应用地址等等；
* 启动 qiankun；

### 创建微应用容器
我们先在主应用中创建微应用的承载容器，这个容器规定了微应用的显示区域，微应用将在该容器内渲染并显示。

我们先设置路由，路由文件规定了主应用自身的路由匹配规则，代码实现如下：
```
// micro-app-main/src/router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '../views/Home.vue'

Vue.use(VueRouter)

const routes = [
  {
    /**
     * path: 路径为 / 时触发该路由规则
     * name: 路由的 name 为 Home
     * component: 触发路由时加载 `Home` 组件
     */
    path: '/',
    name: 'Home',
    component: Home
  },
]
/**
 * 注册路由实例
 * 即将开始监听 location 变化，触发路由规则
 */
const router = new VueRouter({
    mode: "history",
  routes
})

export default router

// micro-app-main/src/main.js
import Vue from 'vue'
import App from './App.vue'
import router from './router'

Vue.config.productionTip = false
// 创建 Vue 实例
// 该实例将挂载/渲染在 id 为 main-app 的节点上
// 此处注意将App.vue & index.html 中的id="app"替换成id="main-app"
new Vue({
  router,
  render: h => h(App)
}).$mount('#main-app')

```
从上面代码可以看出，我们设置了主应用的路由规则，设置了 `Home` 主页的路由匹配规则。
我们现在来设置主应用的布局，我们会有一个菜单和显示区域, 以及实现基座和微应用的显示区域
代码实现如下：
```
// micro-app-main/src/App.vue
<template>
  <div id="main-app">
    <div class="main-menu">
        <div class="main-menu-item" v-for="item in menus" :key="item.key" @click="goto(item.path)">
            <span>
                {{item.title}}
            </span>
        </div>
      <!-- <router-link to="/">Home</router-link> -->
    </div>
    <section class="main-section">
         <!-- 主应用渲染区，用于挂载主应用路由触发的组件 -->
        <router-view v-show="$route.name" />

        <!-- 子应用渲染区，用于挂载子应用节点 -->
        <section v-show="!$route.name" id="frame"></section>
    </section>
  </div>
</template>
<script>
export default {
    name: 'App',
    data() {
        return {
            menus: [
                // 菜单列表
                // {
                //     key: 唯一 Key 值
                //     title: 菜单标题
                //     path: 菜单对应的路径
                // },
                {
                    key: "Home",
                    title: "主页",
                    path: "/",
                }
            ]
        }
    },
    methods: {
        goto(path) {
            this.$router.push(path)
        }
    }
}
</script>
<style >
* {
    margin: 0;
    padding: 0;
}
html, body,#main-app {
    height: 100%;
    width: 100%;
}
</style>
<style scoped>

#main-app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-direction: row;
  height: 100%;
}
.main-section{
    flex-grow: 1;
    height: 100%;
}

.main-menu {
  width: 50px;
  background: rgba(0,18,45,.86);
}
.main-menu-item{
    width: 100%;
    height: 50px;
    line-height: 50px;
    font-size: 14px;
    font-weight: 600;
    text-align: center;
    color: white;
    cursor: pointer;
}
</style>

```
* 我们来分析一下上面的代码：

主应用渲染区。在触发主应用路由规则时（由路由配置表的 $route.name 判断），将渲染主应用的组件；
```
<router-view v-show="$route.name" />
```
微应用渲染区。在未触发主应用路由规则时（由路由配置表的 $route.name 判断），将渲染微应用节点；
```
<section v-show="!$route.name" id="frame"></section>
```
从上面的分析可以看出，我们使用了在路由表配置的 name 字段进行判断，判断当前路由是否为主应用路由，最后决定渲染主应用组件或是微应用节点。
最后主应用的实现效果如下图所示：


缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图
### 注册微应用
在构建好了主框架后，我们需要使用 qiankun 的 registerMicroApps 方法注册微应用，代码实现如下：

```
// micro-app-main/src/micro/apps.js
// 此时我们还没有微应用，所以 apps 为空
const apps = [];

export default apps;

// micro-app-main/src/micro/index.js
// 一个进度条插件
import NProgress from "nprogress";
import "nprogress/nprogress.css";
import { message } from "ant-design-vue";
import {
  registerMicroApps,
  addGlobalUncaughtErrorHandler,
  start,
} from "qiankun";

// 微应用注册信息
import apps from "./apps";

/**
 * 注册微应用
 * 第一个参数 - 微应用的注册信息
 * 第二个参数 - 全局生命周期钩子
 */
registerMicroApps(apps, {
  // qiankun 生命周期钩子 - 微应用加载前
  beforeLoad: (app) => {
    // 加载微应用前，加载进度条
    NProgress.start();
    console.log("before load", app.name);
    return Promise.resolve();
  },
  // qiankun 生命周期钩子 - 微应用挂载后
  afterMount: (app) => {
    // 加载微应用前，进度条加载完成
    NProgress.done();
    console.log("after mount", app.name);
    return Promise.resolve();
  },
});

/**
 * 添加全局的未捕获异常处理器
 */
addGlobalUncaughtErrorHandler((event) => {
  console.error(event);
  const { message: msg } = event;
  // 加载失败时提示
  if (msg && msg.includes("died in status LOADING_SOURCE_CODE")) {
    message.error("微应用加载失败，请检查应用是否可运行");
  }
});

// 导出 qiankun 的启动函数
export default start;
```
从上面可以看出，我们的微应用注册信息在 apps 数组中（此时为空，我们在后面接入微应用时会添加微应用注册信息），然后使用 qiankun 的 registerMicroApps 方法注册微应用，最后导出了 start 函数，注册微应用的工作就完成啦！
### 启动主应用
我们在注册好了微应用，导出 start 函数后，我们需要在合适的地方调用 start 启动主应用。

我们一般是在入口文件启动 qiankun 主应用，代码实现如下：

```
// micro-app-main/src/main.js
//...
import startQiankun from "./micro";

startQiankun();
```
最后，启动我们的主应用，效果图如下：
缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图
到这一步，我们的主应用基座就创建好啦


## 四、接入微应用
---
我们现在的主应用基座只有一个主页，现在我们需要接入微应用。


qiankun 内部通过 import-entry-html 加载微应用，要求微应用需要导出生命周期钩子函数（见下图）。

缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图
图片去picture里面找

从上图可以看出，qiankun 内部会校验微应用的生命周期钩子函数，如果微应用没有导出这三个生命周期钩子函数，则微应用会加载失败。

如果我们使用了脚手架搭建微应用的话，我们可以通过 webpack 配置在入口文件处导出这三个生命周期钩子函数。如果没有使用脚手架的话，也可以直接在微应用的 window 上挂载这三个生命周期钩子函数。

现在我们来接入我们的各个技术栈微应用吧！

注意，下面的内容对相关技术栈 API 不会再有过多介绍啦，如果你要接入不同技术栈的微应用，最好要对该技术栈有一些基础了解。

## 五、接入Vue微应用
---
我们在主应用的同级目录（micro-app-main 同级目录），使用 vue-cli 先创建一个 Vue 的项目，在命令行运行如下命令：
```
$ vue create micro-app-vue
```
在新建项目完成后，我们创建几个路由页面再加上一些样式，最后效果如下:
缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图
### 注册微应用
在创建好了 Vue 微应用后，我们可以开始我们的接入工作了。首先我们需要在主应用中注册该微应用的信息，代码实现如下：

```
// micro-app-main/src/micro/apps.js
const apps = [
    /**
     * name: 微应用名称 - 具有唯一性
     * entry: 微应用入口 - 通过该地址加载微应用
     * container: 微应用挂载节点 - 微应用加载完成后将挂载在该节点上
     * activeRule: 微应用触发的路由规则 - 触发路由规则后将加载该微应用
     */
    {
      name: "VueMicroApp",
      entry: "//localhost:8090",
      container: "#frame",
      activeRule: "/vue",
    },
  ];
  
  export default apps;
```
通过上面的代码，我们就在主应用中注册了我们的 Vue 微应用，进入 /vue 路由时将加载我们的 Vue 微应用。

我们在菜单配置处也加入 Vue 微应用的快捷入口，代码实现如下：

```
// micro-app-main/src/App.vue
data() {
        return {
            menus: [
                // 菜单列表
                // {
                //     key: 唯一 Key 值
                //     title: 菜单标题
                //     path: 菜单对应的路径
                // },
                {
                    key: "Home",
                    title: "主页",
                    path: "/",
                },
                {
                    key: "VueMicroApp",
                    title: "Vue 主页",
                    path: "/vue",
                },
                {
                    key: "VueMicroAppList",
                    title: "Vue 列表页",
                    path: "/vue/list",
                }
            ]
        }
    }
```

菜单配置完成后，我们的主应用基座效果图如下:
缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图

### 配置微应用
在主应用注册好了微应用后，我们还需要对微应用进行一系列的配置。首先，我们在 Vue 的入口文件 main.js 中，导出 qiankun 主应用所需要的三个生命周期钩子函数，代码实现如下：

```
// micro-app-vue/src/main.js
import Vue from "vue";
import VueRouter from "vue-router";
import App from "./App.vue";
import routes from "./router";
import store from "./store";


import "./public-path";
Vue.use(VueRouter);
Vue.config.productionTip = false;

let instance = null;
let router = null;

/**
 * 渲染函数
 * 两种情况：主应用生命周期钩子中运行 / 微应用单独启动时运行
 */
function render() {
  // 在 render 中创建 VueRouter，可以保证在卸载微应用时，移除 location 事件监听，防止事件污染
  router = new VueRouter({
    // 运行在主应用中时，添加路由命名空间 /vue
    base: window.__POWERED_BY_QIANKUN__ ? "/vue" : "/",
    mode: "history",
    routes,
  });

  // 挂载应用
  instance = new Vue({
    router,
    store,
    render: (h) => h(App),
  }).$mount("#app");
}

// 独立运行时，直接挂载应用
if (!window.__POWERED_BY_QIANKUN__) {
  render();
}

/**
 * bootstrap 只会在微应用初始化的时候调用一次，下次微应用重新进入时会直接调用 mount 钩子，不会再重复触发 bootstrap。
 * 通常我们可以在这里做一些全局变量的初始化，比如不会在 unmount 阶段被销毁的应用级别的缓存等。
 */
export async function bootstrap() {
  console.log("VueMicroApp bootstraped");
}

/**
 * 应用每次进入都会调用 mount 方法，通常我们在这里触发应用的渲染方法
 */
export async function mount(props) {
  console.log("VueMicroApp mount", props);
  render(props);
}

/**
 * 应用每次 切出/卸载 会调用的方法，通常在这里我们会卸载微应用的应用实例
 */
export async function unmount() {
  console.log("VueMicroApp unmount");
  instance.$destroy();
  instance = null;
  router = null;
}

```

分析代码：
webpack 默认的 publicPath 为 "" 空字符串，会基于当前路径来加载资源。我们在主应用中加载微应用时需要重新设置 publicPath，这样才能正确加载微应用的相关资源。
* public-path.js 具体实现形式

```
// micro-app-vue/src/public-path.js
if (window.__POWERED_BY_QIANKUN__) {
    // 动态设置 webpack publicPath，防止资源加载出错
    // eslint-disable-next-line no-undef
    __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
  }
```
我们还需要配置 webpack，使 main.js 导出的生命周期钩子函数可以被 qiankun 识别获取。
```
const path = require('path');

module.exports = {
  devServer: {
    // 监听端口
    port: 9080,
    // 关闭主机检查，使微应用可以被 fetch
    disableHostCheck: true,
    // 配置跨域请求头，解决开发环境的跨域问题
    headers: {
      'Access-Control-Allow-Origin': '*',
    },
  },
  configureWebpack: {
    resolve: {
      alias: {
        '@': path.resolve(__dirname, 'src'),
      }
    },
    output: {
      // 微应用的包名，这里与主应用中注册的微应用名称一致
      library: "VueMicroApp",
      // 将你的 library 暴露为所有的模块定义下都可运行的方式
      libraryTarget: 'umd',
      // 按需加载相关，设置为 webpackJsonp_VueMicroApp 即可
      jsonpFunction: `webpackJsonp_VueMicroApp`,
    }
  }
}
```
我们需要重点关注一下 output 选项，当我们把 libraryTarget 设置为 umd 后，我们的 library 就暴露为所有的模块定义下都可运行的方式了，主应用就可以获取到微应用的生命周期钩子函数了。

在 vue.config.js 修改完成后，我们重新启动 Vue 微应用，然后打开主应用基座 http://localhost:9999。我们点击左侧菜单切换到微应用，此时我们的 Vue 微应用被正确加载啦！（见下图）


缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图


此时我们打开控制台，可以看到我们所执行的生命周期钩子函数（见下图）

缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图缺图


多次点击已经正在加载的微应用的路由，控制台报错，这个路由需要处理一下。















## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：[https://segmentfault.com/a/1190000022631614](https://segmentfault.com/a/1190000022631614)