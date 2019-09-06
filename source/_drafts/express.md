---
title: express： 实现本地代码+本地mock+环境接口数据
tags:
---

Author: Echo
Time: 2019-08-14 15:37:18

### express

* 简介
基于 Node.js 平台，快速、开放、极简的 Web 开发框架

简单来说，封装了node中http核心模块，专注于业务逻辑的开发.

express中间件的本质就是请求处理方法，把用户从请求到响应的整个过程分发到多个中间件中去处理，提高代码灵活性，动态可扩展
* 安装
```
& npm install express --save-dev
```

>以vue项目为例
在根目录下新建api文件->server.js文件。


###### 安装其他依赖: 
* body-parser
  一个HTTP请求体解析中间件，使用这个模块可以解析JSON、Raw、文本、URL - encoded格式的请求体，Express框架中就是使用这个模块做为请求体解析中间件。

* request: Request - Simplified HTTP client
* klaw-sync：klaw-sync is a Node.js recursive and fast file system walker

实例代码如下：

```
const express = require('express');
const bodyParser = require('body-parser');

const request = require('request');
const path = require('path');
const walk = require('klaw-sync');
const config = require("../vue.config");


const origin_proxy_url = 'http://origin_proxy_url.jdcloud.com'; //测试接口url
const local_proxy_port = 3002;                                  //本地server端口
const local_proxy_url = `http://localhost:${local_proxy_port}`; //本地接口url


const app = express();

//  创建 application/x-www-form-urlencoded 解析 :解析UTF-8的编码的数据
app.use(bodyParser.urlencoded({ extended: false })); 
// 当设置为false时，会使用querystring库解析URL编码的数据；当设置为true时，会使用qs库解析URL编码的数据。后没有指定编码时，使用此编码。默认为true


// create application/json parser
app.use(bodyParser.json());
app.use(function (req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});

let _existRoutes = [];
app.use((req, res, next) => { //TODO post和上传还有问题
  const { url, body, method } = req;
  if (!~_existRoutes.indexOf(req.path)) {
    const rurl = origin_proxy_url.replace(/\/$/, '') + url;
    let r = method === 'POST'
      ? request.post({ url: rurl, form: body }, (err, httpRes, reqBody) => {
        console.log(err, reqBody, body)
      })
      : request(rurl);
    console.log(`本地未定义的请求，跳转到 ${method} ${rurl}`);
    req.pipe(r).pipe(res);
    return;
  }
  next();
});

//遍历本目录下的 *.api.js
walk(path.resolve('./'))
  .filter(p => /\.api\.js$/.test(p.path))
  .map(p => p.path)
  .forEach(part => require(part)(app));


//全局配置，只在应用启动时读取一次


//记录注册过的路由
_existRoutes = app._router.stack.filter(s => s.route).map(s => s.route.path);

app.listen(local_proxy_port, () => {
  console.log(`\n\n local server running at ${local_proxy_url} \n\n`);
});
```

## 运行express服务器

```
"scripts": {
    "serve": "vue-cli-service serve", // 启动本地项目： 如果配置了代理，请求的是本地代码+测试接口

    // express 相关命令
    "server": "nodemon api/server.js", //  本地express服务器
    "devlocal": "shell-exec --colored-output \"npm run serve --local\" \"npm run server\"" 
    // 启动本地项目: 本地代码+ 本地mock接口/测试接口
  },
```
* 安装运行依赖
```
$ npm install shell-executor --save-dev
$ npm install nodemon --save-dev
```
> shell-executor: A small nodejs module to execute shell commands in parallel
> nodemon: 用来监视node.js应用程序中的任何更改并自动重启服务,非常适合用在开发环境中

