---
title: 优化项目的实用技巧
date: 2020-05-08 15:20:06
tags:
---



## 一些有意思的loader 和 plugins
---

#### 如何自动加载
`require.context`：根据自定的规则获取需要的内容。

> 解决困扰：新增或减少页面每次需要更改路由配置？

* 以vue项目为例，实现动态配置路由。

在vue的component中按照如下格式配置页面。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-47016d33b4647d29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> mode (模块名称)
>> index.routes.js (该模块下路由解析配置)
>> pages (文件统一名称)
>>> mode.index.vue (模块下默认页面)
>>> mode.resource.vue  (模块下其他页面)

在mode中的index.routes.js中配置路由解析代码,会自动生成routes
```
// 第1个参数： 路径
// 第2个参数: 是否遍历1参数下面的子文件夹
// 第3个参数： 正则匹配对应后缀的文件

let r = require.context('./pages', false, /.vue/);
// r是一个func,直接调用会报错。
let routesArr = [];
r.keys().forEach((key) => {
  console.log(r(key))
  let _keyArr = key.split('.');
  if (key.indexOf('index') != -1) {
    routesArr.push ({
      path: _keyArr[1],
      name: _keyArr[1],
      component: r(key).default
    })
  } else {
    routesArr.push ({
      path: `${_keyArr[1]}/${_keyArr[2]}`,
      name: _keyArr[1],
      component: r(key).default
    })
  }
})
export default routesArr
```
在总路由地址下引用各个模块下面的路由：

在router > index.js配置如下代码。
```
let r = require.context('./components', true, /\.routes\.js/);
let routesArr: any[] = [];
r.keys().forEach((key) => {
  console.log(r(key));
  routesArr = routesArr.concat(r(key).defalut);
})
export const routes = [
  {
    path: "/",
    name: 'Home',
    components:Home
  },
  ...routesArr
];
```
缺点： 
  1.不能按需引入
  2.不能实现动态路由。
优点：
  1.规范项目结构+文件命名+路由命名
  2.减少配置路由的困扰

## 如何优化打包
---


#### Dll优化
原理： 减少处理内容和步骤
js - 第三方库： vue / vue-router / axios /loadsh (只压缩打包一次,缓存到静态文件中)

步骤： 预处理第三方库 -> 打包其他文件。

* 以vue项目为例，封装Dll优化。

在vue项目的bulid文件下新建webpack.dll.js文件
```
var path = require('path')
var webpack = require('webpack')
var config = require('../config')

module.exports = {
  entry: {
    // 定义提前处理的文件
    vendor: ['vue/dist/vue.esm.js', 'vue-router']
  },
  output: {
    // 将预处理打包好的文件资源，放在静态文件夹中。
    path: path.join(__dirname, '../static/js'),
    filename: '[name].dll.js',
    library: '[name]_library'
  },
  plugins: [
    // 在当前目录下生成一个通知，命名为[name]_manifest.json
    new webpack.DllPlugin({
      path:path.join(__dirname,'.', '[name]-manifest.json'),
      name: '[name]_library' // 对应output中的library

    })
  ]
}
```
在package.json 的 `scripts`中配置npm run dll 命令
```
"dll": "webpack --config build/webpack.dll.js"
```

然后执行`npm run dll`命令看一下打包所需要的时间
![image.png](https://upload-images.jianshu.io/upload_images/11846892-fafb831343c335b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


然后在执行一下`npm run build` 命令查看一下原始打包所需要的时间
![image.png](https://upload-images.jianshu.io/upload_images/11846892-d5d9ff566fdbde19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


执行`npm run dll `命令之后会发现在`bulid`文件夹下生成了
`vendor-manifest.json`,在`static/js`下生成了`vendor.dll.js`。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-4cf356217f7b1eb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/11846892-4e1610e26c92530e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后在`bulid/webpack.prod.conf.js`中配置正常打包下的文件。

```
plugins: [
    new webpack.DllReferencePlugin({
      //context: 在html中引入的路径。
      context: path.join(__dirname, '..'),
      manifest: require('./vendor-manifest.json')
    })
]
```
最后在执行一下`npm run build` 命令查看一下dll处理之后打包所需要的时间
![image.png](https://upload-images.jianshu.io/upload_images/11846892-cd9552fdd529d347.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


很完美的节约了一些时间，时间就是金钱，时间就是生命。
#### 如何开发插件与loader
---

> 示例问题：项目vuex过大，导致打包结果过大。
> 处理： 按组件异步加载vuex

在src/store文件夹下index文件中存储首屏加载的vuex数据;
在src/store/model/.. 文件夹下存储其他模块需要懒加载的vuex数据;
![image.png](https://upload-images.jianshu.io/upload_images/11846892-5b5262f39bc46f2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在src下新建vuePlugins/index封装插件
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8799f362b4bdfdff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

示例代码如下：
```
let a = {
  install:function(vue) {
    vue.mixin({
      beforeCreate:function(){
        if (this.$options.isVuex) {
          // 在对应页面中配置store
          // store中存储需要按需加载的vuex相关的js文件
          let store = this.$options.store;
          // 动态引入vuex
          // 注意：import的参数不能传入变量。
          import('../store/model/'+ store).then((res) => {
            // registerModul方法是vuex的方法;
            this.$store.registerModule(store, res.default);
          });
        }
      }
    })
  }
}
export default a;
```
在main.js引入插件
```
import Vuepl from "./vuePlugins";
Vue.use(Vuepl);
```
在需要懒加载vuex的页面配置
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c4e24d770a72ec58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
isVuex:true;
store: 'modelIndex';
```

👌了！以上就是vuex实现按需加载的栗子。

