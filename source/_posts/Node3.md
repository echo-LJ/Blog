---
title: NO1:使用 Node.js + Express 开发服务端
date: 2020-07-01 10:00:00
tags: Node
---
<meta name="referrer" content="no-referrer"/>

## 一、 准备工作
---
#### 1.安装Express
```
$ npm install express -g
$ npm install express-generator -g
```

#### 2.初始化项目
```
$ cd /Users/SPRINT/Desktop 进入桌面
$ express 项目名称
```
项目名称我们指定为APIServer，从项目名称可以看出 我们是模拟服务器API
在这里我们将提供一个获取用户详情接口 并输出JSON数据。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-f958d66e6ce428e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在终端最后位置 看到输出两个命令

```
install dependencies:
   $ cd APIServer && npm install //告诉我们进入项目根目录 执行npm install安装依赖模块

run the app:
   $ DEBUG=APIServer:* npm start //告诉我们启动服务器
```

执行如下命令：
```
$ cd APIServer //进入项目根目录
$ npm install  //安装依赖
```
回到项目中，会看到一个APIServer目录
> bin: 用来启动应用（服务器）
 >public: 存放静态资源目录
 >outes：路由用于确定应用程序如何响应对特定端点的客户机请求，包含一个 URI（或路径）和一个特定的 HTTP 请求方法（GET、POST 等）。每个路由可以具有一个或多个处理程序函数，这些函数在路由匹配时执行。
>views: 模板文件所在目录 文件格式为.jade
目录app.js程序main文件 这个是服务器启动的入口

![image.png](https://upload-images.jianshu.io/upload_images/11846892-5d35fab3915217e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 二、 启动服务器
---
首先启动服务器
```
$ npm start
```
![image.png](https://upload-images.jianshu.io/upload_images/11846892-9bac37c86b235821.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在浏览器中访问 [http://localhost:3000/](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A3000%2F)
## 三、 基本使用
---
打开`app.js` 这里介绍下主要代码
```
  var express = require('express');
  var path = require('path');
  var favicon = require('serve-favicon');
  var logger = require('morgan');
  var cookieParser = require('cookie-parser');
  var bodyParser = require('body-parser');

  var app = express();


///=======路由信息 （接口地址）开始 存放在./routes目录下===========//
  var routes = require('./routes/index');//home page接口
  var users = require('./routes/users'); //用户接口

  app.use('/', routes); //在app中注册routes该接口 
  app.use('/users', users);//在app中注册users接口
///=======路由信息 （接口地址 介绍===========//



///=======模板 开始===========//
// view engine setup
  app.set('views', path.join(__dirname, 'views'));
  app.set('view engine', 'jade');
///=======模板 结束===========//
```
当我们在浏览器中 访问 [http://localhost:3000/](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A3000%2F) 调用的就是index中的接口
我们打开index.js就可以看到该接口的定义：
```
var express = require('express');
var router = express.Router();


//定义一个get请求 path为根目录
/* GET home page. */
router.get('/', function(req, res, next) {
    res.render('index', { title: 'Express' });
});

module.exports = router;
```
定义一个路由的基本格式为：

```
app.METHOD(PATH, HANDLER)

```

其中：
> app 是 express 的实例。
> METHOD是 [HTTP 请求方法](https://links.jianshu.com/go?to=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FHypertext_Transfer_Protocol)。
PATH 是服务器上的路径。
HANDLER 是在路由匹配时执行的函数。
以上的定义代表
在根路由 (/) 上（应用程序的主页）对 GET 请求进行响应：

是不是明白了？

如果我们想要实现一个获取用户信息接口该怎么写呢？
新建`routes\user.js`文件
定义一个User模型

```
function User() {
      this.name;
      this.city;
      this.age;
}
module.exports = User;

```
修改`users.js`文件
```
var express = require('express');
var router = express.Router();
var URL = require('url');  
var User = require('./user');

/* GET users listing. */
router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});

router.get('/getUserInfo', function(req, res, next) {

  var user = new User();
// 获取url参数 依赖于url模块 使用前需要使用  require('url')
  var params = URL.parse(req.url, true).query;

if(params.id == '1') {

  user.name = "ligh";
  user.age = "1";
  user.city = "北京市";

}else{  
  user.name = "SPTING";
  user.age = "1";
  user.city = "杭州市";
}

var response = {status:1,data:user};
res.send(JSON.stringify(response));

});
module.exports = router;

```

由于users.js路由信息已经在app.js注册
停止服务器 重新start服务器即可直接访问
```
$ npm start
```

调用方式`http://localhost:3000/users/getUserInfo?id=1`
![image.png](https://upload-images.jianshu.io/upload_images/11846892-2fef11908aa7525f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

----

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

你可能需要：[NO2:Node JS + MySQL CRUD Workshop : Insert, Update, Select, Delete](https://www.jianshu.com/p/02085294b6ed)
