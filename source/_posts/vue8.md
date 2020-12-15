---
title: 搭建Vue的SSR服务端渲染
date: 2018-07-01 10:00:00
tags: Vue
---

<meta name="referrer" content="no-referrer"/>

## SSR是什么
---
`SSR`：Server Side Rendering
服务端渲染，由服务器进行渲染并返回给客户端渲染完成的html
* 优点
超快的响应速度
易做SEO
* 缺点
增加服务器压力
* 主流框架
Next.js —— React的SSR方案
Nuxt.js —— Vue的SSR方案
### SPA是什么
---
SPA：single page application
按照字面意思就是单页面应用，通俗点就是整个网站由一个html页面构成。

######  传统的vue/react项目纯浏览器渲染步骤
1. 浏览器输入url  -> 发送请求到服务器
2. 服务器接收到请求 -> 发送项目的index.html + app.bundle.js文件给浏览器
3. 浏览器执行js,生成dom，渲染dom,发送请求，接收请求，解析数据，操作数据，重新渲染

* SPA缺点 
1. 如果没有进行异步请求，首屏加载过慢。（因为要一次性加载多种依赖和包）
2.缺少SEO， 难以进行搜索引擎优化（对于爬虫来说，它仅仅获取到了2个标签，而没有页面真实呈现内容的信息）
3. 性能问题
* SPA优点
1. 带来接近原生的体验
2. 前后端分离
3. 服务器压力小 响应速度快

######  进行ssr的vue/react项目浏览器渲染步骤
1. 浏览器输入url  -> 发送请求到服务器
2. 服务器(node服务)接收到请求 -> 解析对应的js文件，生成对应的html->发送给浏览器
3. 浏览器接收并渲染html

## SSR需要哪些配置
![image.png](https://upload-images.jianshu.io/upload_images/11846892-5d9692de712dd7b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 搭建Vue的SSR服务端渲染
---

在vue项目过中安装vue-server-renderer
```
 $ npm i vue-server-renderer
$ npm i server
```
在vue项目中创建server.js文件
server.js文件的内容为：
```
/* server.js */
const Vue = require('vue');
const server = require('express')();
const renderer = require('vue-server-renderer').createRenderer();
const fs = require('fs');

function createApp(url) {
  if (url == '/') {
    url = '/index'
  }
  let json = fs.readFileSync(`json${url}.json`,'utf_8');
  let template = fs.readFileSync(`template${url}.html`,'utf_8');
  return new Vue({
    template: template,
    data: JSON.parse(json).data
  })
}
// 响应路由请求
server.get('*', (req, res) => {
  if (req.url !=='/favicon.ico') {
    const app = createApp(req.url);
    renderer.renderToString(app, (err, html) => {
      if (err) { return res.state(500).end('运行时错误') }
      res.send(html);
    });
  }
});

// 服务器监听地址
server.listen(8080, () => {
    console.log('服务器已启动！')
});
```
目录结构
![image.png](https://upload-images.jianshu.io/upload_images/11846892-73a701ecb005919f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/11846892-7853336351f039c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
/* index.js */
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Index</title>
</head>
<body>
  {{a}}
</body>
</html>
/* index.json */
{
  "data": {"a": 1}
}
```

执行命令
```
$ node server.js
```
打开浏览器，地址栏输入：

```
localhost:8080
```

我们可以看到，页面加载成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-94029c331dae155a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/11846892-106520d374bea6f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 对原有的Vue项目改造成SSR
---
1. 在src文件下新建`server.js` + `client.js`
2. 在根目录下新建`index.ssr.html`

```
/* index.ssr.html*/
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
<!-- 注意⚠️！！！⬇️下面注释不能少：如果没有，服务器就不知道将生成好的html代码插在什么位置-->
  <!--vue-ssr-outlet-->
  <script type="text/javescript" src="<%= htmlWebpackPlugin.options.files.js %>"></script>
</body>
</html>
```
3. 对router部分进行改造
* 将路由改造成方法
```
// export default router;
export function createRouter(){
  return new VueRouter({
    mode: "history",
    base: process.env.BASE_URL,
    routes
  });
};
```
4. 对main.js进行改造
* 引入createRouter
* 将main.js改造成方法
```
import Vue from "vue";
import App from "./App.vue";
// import router from "./router";
import { createRouter } from "./router";
const router = createRouter();
// new Vue({
//   router,
//   store,
//   render: h => h(App)
// }).$mount("#app");
export function createApp(){
  const app = new Vue({
    router,
    store,
    render: h => h(App)
  });
  return {app, router}
};
```
** 为什么将main.js和router改造成方法？ 回答：方便调用！**
5. server.js
由于sever.js是在服务端运行，我们将代码形成node格式
```
import { createApp } from "./main";
// context = req (服务端的request)
export function context=>{
  return new Promise((resolve, reject)=> {
    const {app, router} = createApp();
    // 将当前的请求路径添加到路由表中，
    router.push(context.url);
    router.onReady(() => {
      const matchCcmponents = router.getMatchedComponents(path);
      if (!matchCcmponents.length) {
        return reject({code: 404})
      }
      resolve(app)
    }, reject);
  })
};
```

6. client.js
```
import { createApp } from "./main";
const {app, router} = createApp();
router.onReady(() => {
  // 手动挂载;
  app.$mount('#app');
});
```
7. 在build新建`webpack.buildclinet.js`+`webpack.buildserver.js`
8. webpack.buildserver.js
`webpack.buildserver.js`类似于`webpack.prod.conf.js`
对部分内容进行改造：

```
var VueSSRServerPlugin = require('vue-server-renderer/server-plugin')

 
// 指定entry,传统SPA打包配置文件没有entry,打包时是使用webpack.base.conf.js + webpack.prod.conf.js合并之后的entry
  entry: {
    app: './src/server.js'
  },
  // 打包之后的结果是在服务端运行的。
  target: 'node',
  output: {
    // 打包之后的文件的模块化规范，遵循node的模块化规范
    libraryTarget: "commonjs2"
  },
   new HtmlWebpackPlugin({
      filename: 'index.srr.html',
      template: 'index.srr.html',
      inject: true,
      files: {
        js: 'app.js'
      },
     // 注意⚠️！！！删除掉压缩部分配置代码⬇️：为什么不能压缩，看第2步的解释（你能找到解释么？嘿嘿）！
      // minify: {
        //removeComments: true,
         //collapseWhitespace: true,
        //removeAttributeQuotes: true
      //},
      chunksSortMode: 'dependency'
   }),
  plugins: [
    // 插件作用：对服务端代码进行打包
    new VueSSRServerPlugin(),
 ......
```

9. webpack.buildclient.js
`webpack.buildclient.js`类似于`webpack.prod.conf.js`
对部分内容进行改造：
```
var VueSSRClientPlugin = require('vue-server-renderer/client-plugin')

// 指定entry,传统SPA打包配置文件没有entry,打包时是使用webpack.base.conf.js + webpack.prod.conf.js合并之后的entry
  entry: {
    app: './src/client.js'
  },
// 注意⚠️！！！删掉出口配置output:

  plugins: [
    // 插件作用：对客户端代码进行打包
    new VueSSRClientPlugin(),
 ......
```

10. 增加打包命令

```
"build:client": "webpack --config build/webpack.buildclient.js"
"build:server": "webpack --config build/webpack.buildserver.js"
```
11. 执行打包命令

```
$ npm run build:server
$ npm run build:client
```
打包之后结果
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c96aecf8bdcde96c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/11846892-2bcceeefe888efaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 12 编写server
* 在根目录下新建server文件夹 + `server.js`
server.js
```
const express = require('express');
const server = express();
const {createBundleRenderer} = require('vue-server-renderder');
const path = require('path');
const fs = require('fs');
const serverBundle = require(path.resolve(__dirname, '../dist/vue-ssr-server-bundle.json'));
const clientManifest = require(path.resolve(__dirname, '../dist/vue-ssr-client-manifest.json'));
const template= fs.readFileSync(path.resolve(__dirname, '..dist/index.ssr.html'), 'utf-8');
// 打包出来的json的作用：通知服务器如何分割js,部分js用于客户端执行，部分js用于服务端运行。
const renderer = createBundleRenderer(serverBundle,{
  runInNewContext: false,
  template:template,
  clientManifest:clientManifest
});
// 设置静态目录，以dist文件夹为静态目录，dist文件夹在服务开始之后可以访问
server.use(express.static(path.resolve(__dirname, '../dist')));
// 设置路由
server.get('*',(req,res)=>{
  if (req.url !=='/favicon.ico') {
    const context = {url: req.url};
    // 在项目中生成的html文件巨大，通过流的方式处理大的文件
    const ssrstream = renderer.renderToStream(context);
    let buffers = [];
    ssrstream.on('error', (err) => {
      console.log(err);
    });
    ssrstream.on('data', (data) => buffers.push(data));
    ssrstream.on('end', () => {
      res.end(Buffer.concat(buffers));
    });
  }
});
server.listen(2000);
```

## 13 node 运行server.js

```
$ node server.js
```
项目运行之后，会发现切换页面，都会重新请求页面刷新。

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️
