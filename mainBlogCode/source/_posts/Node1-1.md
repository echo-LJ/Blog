---
title: Node即学即用-笔记(1)
date: 2023-01-05 14:31:38
tags: Nginx
---
<meta name="referrer" content="no-referrer"/>

## Node.js简介

* Node 功能强大，特别是它能在浏览器以外运行 JavaScript。
* Node 是对高性能 V8 引擎的封装（V8 是 Google Chrome 浏览器的 JavaScript 引擎），通过提供一系列优化的 API 类库，使 V8 在浏览器之外依然能高效运行。
* Node 除了用 V8 引擎来解析JavaScript 外，还提供了高度优化的应用库，用来提高服务器效率。
* Node 用的是 CommonJS 模块风格。

## Node.js示例从终端入手

1) 终端输入`node`，即可进行JavaScript代码解析

![node1.png](https://upload-images.jianshu.io/upload_images/11846892-be20eecfa363c88e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2) 终端输入`.help`，会显示帮助菜单

![截屏2023-01-05 下午2.51.20.png](https://upload-images.jianshu.io/upload_images/11846892-922d8ca98727df01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
.clear:它会清除内存中任何变量或闭包，而不需要重启解析器
```
## 编写Node.js服务器程序

```
var http = require('http');
http.createServer(function (req, res) {
res.writeHead(200, {'Content-Type': 'text/plain'});
res.end('Hello World\n');
}).listen(8124, "127.0.0.1");
console.log('Server running at http://127.0.0.1:8124/');
```

在浏览器中访问[http://127.0.0.1:8124/](http://127.0.0.1:8124/)可以看到页面中显示`Hello World`

**代码解析**

1）先通过 require 方法把 HTTP 库包含到程序中来，HTTP 库所具有的功能已经赋给了 http 对象。

2）通过调用 HTTP 模块的一个`createServer`方法来创建新的 HTTP 服务器.并传入一个匿名函数作为参数，此函数绑定在
新创建服务器的事件监听器上进行 request 事件处理。每当一个新的访问请求到达 Web 服务器，它都将调用我们指定的函数方法来处理。我们称这类方法为回调（callback）。

举例： 每当通过浏览器访问[http://127.0.0.1:8124/](http://127.0.0.1:8124/)，都会通过匿名函数进行处理请求头，返回`hello world`。

3）首先必须调用 res.writeHead 方法来设置 HTTP 响应头，否则就不能返回真实内容给客户端。设置状态代码为 200（表示 HTTP 状态代码“200 OK”），并且传入一段 HTTP 头描述。在本例中，只指定了 Content-type。

4)end 方法将在把正文内容发送给客户端后进行关闭连接。



## 创建一个聊天服务器

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [Node即学即用](https://awesome-programming-books.github.io/nodejs/Node%E5%8D%B3%E5%AD%A6%E5%8D%B3%E7%94%A8.pdf)
