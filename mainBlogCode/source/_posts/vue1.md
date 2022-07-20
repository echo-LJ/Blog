---
title: 基于 vue-cli 打造属于自己的 UI 库
date: 2017-03-01 10:00:00
tags: Vue
---

<meta name="referrer" content="no-referrer"/>
 
## 知识前置
---
我们可能习惯了在一个 vue 里面引入组件的方式，所以先这里要巩固一下全局引入组件的方式。举个栗子🌰，一般我们的用法是这样的：
```
import Loading from '../components/loading'
// 方法一：name 是组件的名字
Vue.component(Loading.name, Loading)
// 方法二：前提是 Loading 有提供 install 这个方法
Vue.use(Loading);
```
## 基于vue-cli3打造组件库
---
### 搭建目录

#### 快速创建项目
* `vue-cli3`
```
$ vue create projectName
```
### 修改目录结构

*  把 `src` 目录名字改成 `examples`，这是用于展示组件示例的.
*  在根目录下新建一个 `packages` 文件夹，这是用来放组件的.
![1ll.png](https://upload-images.jianshu.io/upload_images/11846892-ded6c43e63e7097f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    你可能会问为什么要建这样的目录结构，问得好，原因很简单，因为别人是这样做的，所以借鉴（模仿）喽。。。
当我们水平不够的时候，模仿是一种强大的学习能力👏。

### 添加配置文件

小改了一下目录之后，你会惊奇的发现项目不能运行了,没关系，这很正常，毕竟 src 都不见了，路径啥的肯定得报错。所以现在我们来解决这个问题。 在根目录下新建一个 `vue.config.js` 文件（新项目是没有这个文件的），并写入以下内容：

```
const path = require('path')
module.exports = {
  // 修改 pages 入口
  pages: {
    index: {
      entry: 'examples/main.js', // 入口
      template: 'public/index.html', // 模板
      filename: 'index.html' // 输出文件
    }
  },
  // 扩展 webpack 配置
  chainWebpack: config => {
    // @ 默认指向 src 目录，这里要改成 examples
    // 另外也可以新增一个 ~ 指向 packages
    config.resolve.alias
      .set('@', path.resolve('examples'))
      .set('~', path.resolve('packages'))

    // 把 packages 和 examples 加入编译，因为新增的文件默认是不被 webpack 处理的
    config.module
      .rule('js')
      .include.add(/packages/).end()
      .include.add(/examples/).end()
      .use('babel')
      .loader('babel-loader')
      .tap(options => {
        // 修改它的选项...
        return options
      })
  }
}

```

上面的注释应该都写的挺明了，主要就是`修改别名`、`修改入口文件`以及把新文件加入 `webpack` 编译这几个步骤。然后我们再运行一下程序就可以跑得通了。至于为什么这么配置、或者怎么配置，不了解的同学可以去 [Vue Cli](https://cli.vuejs.org/zh/guide/) 官网看下，上面写的是清清楚楚、明明白白.

### 编写组件

一个组件库没有组件怎么行呢，所以我们要先写个 `test` 组件（你可以随便写，这不重要）,ok👌。
我们先在 `packages` 目录下新建一个 `test` 文件夹，再在 `test` 文件夹下下面新建一个 src 文件夹，在 src 文件夹下面新建一个 `test.vue` 组件，大概长下面这样子👇：
![](https://upload-images.jianshu.io/upload_images/11846892-72c3c6fb69c57a58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
<!--test.vue-->
<template>
  <div class="echojoy-test" @click="handleClick">{{ num }}</div>
</template>

<script>
export default {
  name: 'EchojoyTest',
  data () {
    return {
      num: 0
    }
  },
  methods: {
    handleClick () {
      this.num++
    }
  }
}
</script>

<style >
.echojoy-test {
  width: 100px;
  height: 100px;
  line-height: 100px;
  border-radius: 50%;
  font-size: 30px;
  text-align: center;
  background: #24292e;
  color: white;
}
</style>


```

⚠️这里主要强调一点，就是 `name` 这个名字尤为重要, 否则可能会遇到坑。
* 首先它是必须要写的，可以把它理解为 id，具有唯一标识组件的作用，将来我们可是要通过这个 name 来找到和判定这是什么组件，所以你写的所有组件应该是不重名的；
* 其次这个 name 就是我们最终的标签名，比如这里我们的 name 是 `EchojoyTest`，到时候我们写的标签就长这样 `<echojoy-test></echojoy-test>`，就像 Element 一样，name 是 `ElButton`，用的时候就是 `<el-button></el-button>`。

### 暴露组件

让我们在 `packages/test` 下面新建一个 `index.js` 文件，具体代码如下：

```
// 对外提供对组件的引用，注意组件必须声明 name
import EchojoyTest from './src/test'
// 为组件提供 install 安装方法，供按需引入
EchojoyTest.install = Vue => {
  Vue.component(EchojoyTest.name, EchojoyTest)
}
export default EchojoyTest

```

这步的精髓就在于给组件扩展一个 `install` 方法，至于为什么要扩展这个方法，文章开头已经说到了，是因为 `Vue.use()` 的需要，use 会默认调用 `install` 方法安装，仅此而已。
接着我们在 `packages` 下面也新建一个 `index.js` 文件，注意和上面那个 `index.js` 区别开，上面那个是针对单个组件安装的，这个是针对所有组件全局安装的，先看代码：

```
import EchojoyTest from './test'
// 存储组件列表
const components = [
  EchojoyTest
]
// 定义 install 方法，接收 Vue 作为参数。如果使用 use 注册插件，则所有的组件都将被注册
const install = function (Vue) {
  // 判断是否安装
  if (install.installed) return
  install.installed = true
  // 遍历注册全局组件
  components.map(component => Vue.component(component.name, component))
  // 下面这个写法也可以
  // components.map(component => Vue.use(component))
}

// 判断是否是直接引入文件
if (typeof window !== 'undefined' && window.Vue) {
  install(window.Vue)
}

export default {
  // 导出的对象必须具有 install，才能被 Vue.use() 方法安装
  install,
  // 以下是具体的组件列表
  ...components
}


```

这步的主要作用就是统一导出所有组件及暴露 `install` 方法。之前的 `index.js` 只是安装单个组件，而现在这个 `index.js` 是循环安装所有组件，具体使用就看你是不是要按需引用了。这里给个目录结构方便大家观看：


![4ll.png](https://upload-images.jianshu.io/upload_images/11846892-b539cdd4f6327d1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为这步挺重要的，所以建议好好停下来理解消化一下🤔。。。
当然你可能会问道，为什么这样建目录？还能什么原因，因为 `Element` 是这样（如下图），所以我们这样写，仅此而已。

### 组件测试

ok，组件写完了，接下来我们就在 `examples` 下面测试一下，看看能不能引用成功。 首先在 `examples` 下的 `main.js` 中引入刚刚写好的包，就像下面这样：
```
import EchojoyUI from './../packages'
Vue.use(EchojoyUI);
```
然后把 `examples/components`下面的 `HelloWorld`里面的内容删了，写入自己标签组件，就像下面这样：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-af5876cc2dfddcf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


好了，最后让我们运行一下项目 `npm run  serve`，看看效果，嗯，还凑合吧。

### 库模式打包

在 `vue-cli3` 中我们通过以下命令可以将一个单独的入口打包成一个库：

```
// target: 默认为构建应用，改为 lib 即可启用构建库模式
// name: 输出文件名
// dest: 输出目录，默认为 dist，这里我们改为 lib
// entry: 入口文件路径
vue-cli-service build --target lib --name lib [entry]

```

要注意的是在库模式中，打包出来的库中是不包含 Vue 的。 然后我们修改一下 `package.json` 文件，就像下面这样：

![xxxxx.png](https://upload-images.jianshu.io/upload_images/11846892-202f788eec24a66d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



接着执行 `npm run lib` 就能生成库啦，看看左侧的目录是不是多了个 lib 文件夹，那个就是我们要发布的东西。
![cccc.png](https://upload-images.jianshu.io/upload_images/11846892-0696a5a907bf1d38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



补充下，lib 目录下面的 js 之所以有好几种，是因为有两种规范`（common 和 umd）`、是否压缩`（min）`和映射 `（map）`的区别，暂且知道有这么回事就行，不用深究。

## 发布到npm
---

万事俱备，只欠发布。

1.  完善一下 `README.md` 文档，这个随便写两句就好
2.  修改一下 `package.json` 文件：

```
{ 
  "name": "echojoy-ui",
  "version": "0.1.0",
  "private": true,
  "description": "基于 vue-cli3 的 UI 组件库",
  "main": "lib/xr-ui.umd.min.js",
  "keywords": "echojoy-ui",
  "private": false,
  "license": "MIT"
}

```

3.  在根目录下新建一个 `.npmignore` 文件，内容和 `.gitignore` 差不多：

```
# 这是复制 .gitignore 里面的
.DS_Store
node_modules
/dist

# local env files
.env.local
.env.*.local

# Log files
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Editor directories and files
.idea
.vscode
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw*

# 以下是新增的
# 要忽略目录和指定文件
examples/
packages/
public/
vue.config.js
babel.config.js
*.map
*.html

```

最后执行 `npm login` 登入 npm 账号，再执行 `npm publish` 发布即可，就这么简单的两步就可以，过一会在 `npm` 上就能搜到了。当然前提是你有个 `npm` 账号，没有的话去注册一个吧，很 easy 的，然后还要搜下你的 `npm` 包名是否有人用，有的话就换一个。

## 小试牛刀
---

终于，历尽千辛万苦，我们可以引用自己写的库拉，想想就牛叉。别激动，让我们试验一下，用 `vue create echojoy-ui-use` 另起一个新项目，然后 `npm i echojoy-ui -S`，可以在 `node_modules` 里面看到我们的包大概长这样：
![zzzz.png](https://upload-images.jianshu.io/upload_images/11846892-481e0f1c018096c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



然后在 main.js 引入：

```
import EchojoyUI from "echojoy-ui";
import "echojoy-ui/lib/echojoy-ui.css";
Vue.use(EchojoyUI);
```

这样我们就能在页面中引入组件啦，哈哈哈哈，贼开心，喜上眉梢。。。

```
<echojoy-test></echojoy-test>
```

## ! vue-cli3（vue-cli2）
---
```
 vue init webpack projectName
```
![1111.png](https://upload-images.jianshu.io/upload_images/11846892-51e06302184372b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####  启动项目
`
 npm run dev
`
![4444.png](https://upload-images.jianshu.io/upload_images/11846892-93b0c42320cb0948.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
浏览器输入
```
http://localhost:8080
```
#### 编写组件

在`src`的`components`新建组件`limit`：即文件夹`EchoLimit`->文件`index.vue` + 文件`index+css`
```
// index.vue
<template>
  <div class="echo-limit-container" v-if="visible === false">
    <div class="echo-limit-container-inner">
      <span class="echo-limit-text">
        {{limitTip}}
      </span>
    </div>
  </div>
</template>

<script>

export default {
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    limitTip: {
      type: String,
      default: '没有权限！！'
    },
  },
  name: "EchoLimit" // 这个名字很重要，它就是未来的标签名<echo-limit></echo-limit>
};
</script>
<style>
  @import "./index"
</style>


```
   

⚠️这里主要强调一点，就是 name 这个名字尤为重要，你可以把它理解为 id，具有唯一标识组件的作用，将来我们可是要通过这个 name 来找到和判定这是什么组件，所以你写的所有组件应该是不重名的；其次这个 name 就是我们最终的标签名，比如这里我们的 name 是 EchoLimit，到时候我们写的标签就长这样 <echo-limit></echo-limit>，就像` Element` 一样，name 是 `ElButton`，用的时候就是 <el-button></el-button>。

#### 暴露组件

* 注册单个组件

在 `components` 的 `EchoLimit`下面新建一个 `index.js` 文件，具体代码如下：
// 为组件提供 install 方法，供组件对外按需引入
```
import EchoLimit from "./EchoLimit/index.vue";
EchoLimit.install = Vue => {
  Vue.component(EchoLimit.name, EchoLimit);
};
export default EchoLimit;
```

这步的精髓就在于给组件扩展一个 `install` 方法，至于为什么要扩展这个方法，文章开头已经说到了，是因为 `Vue.use()` 的需要，use 会默认调用 `install` 方法安装。

* 注册全局组件
让我们在 components 下面新建一个 index.js 文件，具体代码如下：
```
// index.js
import EchoLimit from './EchoLimit'
// 所有组件列表
const components = [
  EchoLimit
]
// 定义 install 方法，接收 Vue 作为参数
const install = function (Vue) {
  // 判断是否安装，安装过就不继续往下执行
  if (install.installed) return
  install.installed = true
  // 遍历注册所有组件
  components.map(component => Vue.component(component.name, component))
  // 下面这个写法也可以
  // components.map(component => Vue.use(component))
}

// 检测到 Vue 才执行，毕竟我们是基于 Vue 的
if (typeof window !== 'undefined' && window.Vue) {
  install(window.Vue)
}

console.log(components[0].name) // 控制台输出组件的name,方便引用组件

export default {
  install,
  // 所有组件，必须具有 install，才能使用 Vue.use()
  ...components
}

```

目录结构方便大家观看：
![6666.png](https://upload-images.jianshu.io/upload_images/11846892-146fa983af0cd96a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 组件测试

ok，组件写完了，接下来我们就在 `HelloWorld.vue`下面测试一下，看看能不能引用成功。 首先在 src下的 `main.js` 中引入刚刚写好的包，如下所示：

```
import EchoUI from './components'
Vue.use(EchoUI)
```
在 HelloWorld.vue中引入组件EchoLimit.
```
<template>
  <div>
    <EchoLimit></EchoLimit>
     <echo-limit></echo-limit>
  </div>
</echo-limit>
```
运行项目`npm run dev`,会发现成功了！！!
![999.png](https://upload-images.jianshu.io/upload_images/11846892-8bf68bbc8041c8ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



过程同上！！！！！！！！！

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考 连接：https://www.jianshu.com/p/41cc796488df
参考 链接：https://juejin.im/post/5c95c61f6fb9a070c40acf65
