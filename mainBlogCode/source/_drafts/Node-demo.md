---
title: NO1:使用 Node.js + Express + MySQL 开发服务端，实现接口的增删改查
date: 2020-07-01 10:00:00
tags: Node
---
<meta name="referrer" content="no-referrer"/>


#### 前言
    基于去年发布的几篇关于Node.js的文章，为方便回顾，整合成一篇文章，基于 Node.js + Express 开发服务端，基于 Node JS + MySQL实现接口的增删改查，基于Node.js + mysql+Sequelize实现注册接口及校验逻辑。
⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️首先：基于 Node.js + Express 开发服务端⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️⚠️
## 一、 准备工作
---

使用应用程序生成器工具 [express-generator](https://www.expressjs.com.cn/starter/generator.html) 快速创建应用程序框架

**`1.1 全局安装Express`**

```
npm install express -g
npm install express-generator -g
```
**`1.2 初始化项目`**

```
express  APIServer// 项目名称
```

项目名称我们指定为APIServer，从项目名称可以看出 我们是模拟服务器API。

未来：在这里我们将提供一个获取用户详情接口 并输出JSON数据。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-f958d66e6ce428e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在终端最后位置 看到输出两个命令

```
install dependencies:
   $ cd APIServer && npm install //告诉我们进入项目根目录 执行npm install安装依赖模块

run the app:
   $ DEBUG=APIServer:* npm start //告诉我们启动服务器
```

**`1.3 安装依赖`**

```
cd APIServer //进入项目根目录
npm install  //安装依赖
```

**`1.4 APIServer目录结构说明`**
```
├── bin: 用来启动应用（服务器）
├── public: 存放静态资源目录
├── routes：路由用于确定应用程序如何响应对特定端点的客户机请求，包含一个 URI（或路径）和一个特定的 HTTP 请求方法（GET、POST 等）。每个路由可以具有一个或多个处理程序函数，这些函数在路由匹配时执行。
├── views: 模板文件所在目录 文件格式为.jade
├── app.js: 程序主文件 这个是服务器启动的入口
```

## 二、 启动服务器
---

**`2.1 启动服务器`**

```
npm start
```
执行命令之后，在终端会看到如下所示：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-9bac37c86b235821.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

换图
换图
换图
换图
换图
换图


在浏览器中访问 [http://localhost:3000/](http://localhost:3000/),可以看到浏览器中显示，如图所示：

缺图
缺图
缺图
缺图
缺图
缺图

## 三、 代码介绍&接口实现
---
打开`app.js` 这里介绍下主要代码

```
 var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');
// /=======路由信息 （接口地址）开始 存放在./routes目录下===========//
var indexRouter = require('./routes/index'); // Home page接口
var usersRouter = require('./routes/users'); // 用户接口

var app = express();

// /=======模板 开始==========================================//
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');
// /=======模板 结束==========================================//

app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));

app.use('/', indexRouter); //在app中注册routes该接口 
app.use('/users', usersRouter); //在app中注册users接口

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;

```

当在浏览器中 访问 [http://localhost:3000/](http://localhost:3000/) 调用的就是index中的接口

打开index.js就可以看到该接口的定义：

```
var express = require('express');
var router = express.Router();

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
* app 是 express 的实例。
* METHOD是 [HTTP 请求方法](https://links.jianshu.com/go?to=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FHypertext_Transfer_Protocol)。
PATH 是服务器上的路径。
* HANDLER 是在路由匹配时执行的函数。
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
// URL.parse： 将url字符串，解析成object，便于开发者进行操作。
var params = URL.parse(req.url, true).query;

if(params.id == '1') {

user.name = "张三";
user.age = "1";
user.city = "北京市";

}else{  
user.name = "李四";
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
npm start
```

当在浏览器中 访问 [http://localhost:3000/users/getUserInfo?id=1](http://localhost:3000/users/getUserInfo?id=1) 调用的就是users中的接口，如下所示：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-2fef11908aa7525f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

换图
换图
换图
换图
换图
换图

👇👇👇👇👇接下来：基于 Node JS + MySQL实现接口的增删改查👇👇👇👇👇

## 四、 实现接口的增删改查
---
**`4.1 将项目views文件夹转换为ejs格式`**

将项目views文件夹转换为`ejs`格式:
⚠️：此命令会将代码回退到初始化步骤！
```
$ express --view=ejs APIServer
```
**`4.2 安装MySQL`**
安装MySQL可参考文章：（windows～～嘿嘿！！？）
* [macOS 上安裝 MySQL](https://www.jianshu.com/p/957026a0af8c)
* [菜鸟教程：MySQL 安装](https://www.runoob.com/mysql/mysql-install.html)
扩展：
❓`MAC安装mysql启动 ‘Start MySQL Server’ 没反应`

安装mysql后反复启动很多次,
输完密码还是不好使,卸载重新安装也不行
下面几个命令可以帮助你解决问题
```
$ sudo chown -RL root:mysql /usr/local/mysql
$ sudo chown -RL mysql:mysql /usr/local/mysql/data
$ sudo /usr/local/mysql/support-files/mysql.server start
```

**`4.3 安装相关依赖`**
```
npm install mysql nodemon
```
终端执行命令`npx nodemon`启动项目
```
npx nodemon
```
在浏览器中访问`http://localhost:3000`,可以看到
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8a0a17bbf47833d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
换图换图换图换图=》换成有url的

**`4.4 创建数据库`**
* 安装mysql可视化工具：[Navicat Premium](http://www.navicat.com.cn/products/)

**`4.5 连接数据库 并 通过代码创建数据库 并实现接口的增删改查`**

在`routes/index.js`据你的实际配置修改数据库用户名、及密码及数据库名：
```
// index.js
var express = require('express');
var router = express.Router();
var mysql = require('mysql');
var db = mysql.createConnection({
  host     : '127.0.0.1',
  user     : 'root',
  password : 'xxxxxx', // 密码是自己设置的msysql密码
})

// connect
db.connect((err) => {
  if (err) {
    throw err;
  }
  console.log('Mysql Connected ...')
});

// Create DB
router.get('/createdb', (req,res)=> {
  let sql = 'CREATE DATABASE nodemysql';
  db.query(sql, (err, result) => {
    if (err) throw err;
    console.log(result)
    res.send('database created...')
  })
})
/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;
```

在终端可以看到连接成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-4c400f5a66ee4489.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在浏览器中访问[http://localhost:3000/createdb](http://localhost:3000/createdb),可以看到数据库创建成功！


？**`扩展 `**
注意⚠️⚠️⚠️：因本人配置了本地host`127.0.0.1  localhost.xxxx.test`导致连接失败，index.js中host应该与本地配置保持一致，即可链接成功。
```
var db = mysql.createConnection({
  host     : 'localhost.xxxx.test', // ！！！！！！
  user     : 'root',
  password : 'xxxxxx', // 密码是自己设置的msysql密码
})
```
![image.png](https://upload-images.jianshu.io/upload_images/11846892-a11076e500f9b461.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在终端可以看到
![image.png](https://upload-images.jianshu.io/upload_images/11846892-e2bbf12b1267b7e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
同样打开可视化工具也可以看到`nodemysql`创建成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-34169c49fb70ab88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**`4.6 通过代码创建table`**
```
// router/index.js
var express = require('express');
var router = express.Router();
var mysql = require('mysql');
var db = mysql.createConnection({
  host     : 'localhost',
  user     : 'root',
  password : '1234567w',
  database : 'nodemysql'
})
// Create table
router.get('/createpoststable', (req,res)=> {
  let sql = 'CREATE TABLE posts(id int AUTO_INCREMENT, title VARCHAR(255), body VARCHAR(255), PRIMARY KEY (id))';
  db.query(sql, (err, result) => {
    if (err) throw err;
    console.log(result)
    res.send('Posts table created...')
  })
})
```
在浏览器中访问`http://localhost:3000/createpoststable
`,可以看到table创建成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-9bdffb7b567a8943.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
同样打开可视化工具也可以看到`posts`创建成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-dcc66a46fedf5f63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**`4.7 通过代码向table中插入数据`**
```
// index.js
var express = require('express');
var router = express.Router();
var mysql = require('mysql');
var db = mysql.createConnection({
  host     : 'localhost',
  user     : 'root',
  password : '1234567w',
  database : 'nodemysql'
})
// Inset posts 1 
router.get('/addpost1', (req,res)=> {
  let post = {title: 'Post One', body: 'This is post number one'}
  let sql = 'INSERT INTO posts SET ?';
  let query = db.query(sql, post, (err, result) => {
    if (err) throw err;
    console.log(result)
    res.send('Posts 1 added...')
  })
})
...
```
在浏览器中访问`http://localhost:3000/addpost1`,可以看到插入数据成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-78969b08e73453c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
同样打开可视化工具也可以看到`posts`数据插入成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-05a83c50ec127fe9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`4.8 通过代码读取table中数据`**
* 4.8.1 读取表中全部数据
```
// index.js
...
// SELECT posts 
router.get('/getposts', (req,res)=> {
  let sql = 'SELECT * FROM posts';
  let query = db.query(sql, (err, results) => {
    if (err) throw err;
    console.log(results)
    res.send('Posts fetched...')
  })
})x
...
```
在浏览器中访问`http://localhost:3000/getposts`,可以看到读取数据成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-cfc7f96056b9c9e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

终端可以看到读取的数据：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c37d906b8b1a4715.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 4.8.2 根据id读取表中部分数据
```
//index.js
// SELECT Single post 
router.get('/getpost/:id', (req,res)=> {
  let sql = `SELECT * FROM posts WHERE id = ${req.params.id}`;
  let query = db.query(sql, (err, result) => {
    if (err) throw err;
    console.log(result)
    res.send('Post fetched...')
  })
})
```
在浏览器中访问`http://localhost:3000/getpost/1`  + `http://localhost:3000/getpost/6`,可以看到读取数据成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8ddc897ea7ca94e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

终端可以看到读取的数据：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c5fe383c4a015b47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`4.9 通过代码更新table中数据`**
```
// index.js
// UPDATE Single post 
router.get('/updatepost/:id', (req,res)=> {
  let newTitle = 'Updated Title'
  let sql = `UPDATE posts SET title = '${newTitle}' WHERE id = ${req.params.id}`;
  let query = db.query(sql, (err, result) => {
    if (err) throw err;
    console.log(result)
    res.send('Post updated...')
  })
})
```
在浏览器中访问`[http://localhost:3000/updatepost/1](http://localhost:3000/updatepost/1)
`,可以看到更新数据成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-fb5f75a2454fc15d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同样打开可视化工具也可以看到`posts`数据更新成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8168a3494b1efcd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`4.9 通过代码删除table中数据`**
```
// index.js
// DELETE post 
router.get('/deletepost/:id', (req,res)=> {
  let newTitle = 'Updated Title'
  let sql = `DELETE FROM posts WHERE id = ${req.params.id}`;
  let query = db.query(sql, (err, result) => {
    if (err) throw err;
    console.log(result)
    res.send('Post updated...')
  })
})
```
在浏览器中访问`[http://localhost:3000/deletepost/1](http://localhost:3000/updatepost/1)
`,可以看到删除数据成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-5c34c189dee174cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`4.10 通过代码删除table中数据`**


在`routes/index.js`新增如下内容
```
// SHOW posts 
router.get('/getpostsData', (req,res)=> {
  let sql = 'SELECT * FROM posts';
  let query = db.query(sql, (err, results) => {
    if (err) throw err;
    console.log(results)
    res.render('select', { books:results });
  })
})

```
新建`views/select.ejs`
```
<h1>Books Data</h1>

<table>
  <tr>
    <td>id</td>
    <td>title</td>
    <td>body</td>
  </tr>
  <% books.forEach(function(item) { %>
    <tr>
      <td><%= item.id %></td>
      <td><%= item.title %></td>
      <td><%= item.body %></td>
    </tr>
  <% }); %>
</table>
```
在浏览器中访问`http://localhost:3000/getpostsData`,可以看到数据展示成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-f58fed086c4ac67d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们同样可以使用模版语言实现通过页面进行`增删改查`操作。

👇👇👇👇👇接下来：Node.js + mysql+Sequelize实现注册接口及校验逻辑👇👇👇👇👇

## 五、 实现注册接口及校验逻辑
---

**`5.1 安装依赖`**

安装`mysql2`+`sequelize` +  `jsonwebtoken` + `joi`

* `sequelize`
`sequelize.js` 提供对 [MySQL](http://www.oschina.net/p/mysql)，[MariaDB](http://www.oschina.net/p/mariadb)，[SQLite](http://www.oschina.net/p/sqlite) 和 [PostgreSQL](http://www.oschina.net/p/postgresql) 数据库的简单访问，通过映射数据库条目到对象，或者对象到数据库条目。简而言之，就是 ORM（Object-Relational-Mapper）。Sequelize.js 完全是使用 JavaScript 编写，适用于 Node.js 的环境。

```
 $ npm install --save sequelize bcrypt mysql2 joi jsonwebtoken
```

**`5.2 添加+修改配置文件`**

`修改app.js`

```
// app.js
var indexRouter = require('./routes/index');
// var usersRouter = require('./routes/users');

app.use('/', indexRouter);
// app.use('/users', usersRouter);
```

`修改bin/www`

```
const {sequelize} = require('../models')
sequelize.sync({force: false})
  .then(() => {
    server.listen(port);
    console.log(`Server started on port ${config.port}`)
  }).catch(function (err) {
    console.log('failed: ' + err);
});
// server.listen(port);
```

`新建models/index.js + models/user.js`:配置各种数据库模型类

```
//models/index.js
const fs = require('fs')
const path = require('path')
const Sequelize = require('sequelize')
const config = require('../config/config')
const db = {}

const sequelize = new Sequelize(config.database, config.username, config.password, {
  host: config.host,
  dialect: 'mysql',
  pool: {
      max: 5,
      min: 0,
      idle: 30000
  }
});

fs
  .readdirSync(__dirname)
  .filter((file) =>
    file !== 'index.js'
  )
  .forEach((file) => {
    const model = require(path.join(__dirname, file))(sequelize, DataTypes)
    db[model.name] = model;
  })

Object.keys(db).forEach(function (modelName) {
  if ('associate' in db[modelName]) {
    db[modelName].associate(db)
  }
})

db.sequelize = sequelize
db.Sequelize = Sequelize

module.exports = db
```
```
//models/user.js
const Promise = require('bluebird')
const bcrypt = Promise.promisifyAll(require('bcrypt'))

// function hashPassword (user, options) {
//   const SALT_FACTOR = 8

//   if (!user.changed('password')) {
//     return
//   }

//   return bcrypt
//     .genSaltAsync(SALT_FACTOR)
//     .then(salt => bcrypt.hashAsync(user.password, salt, null))
//     .then(hash => {
//       user.setDataValue('password', hash)
//     })
// }

module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('User', {
    email: {
      type: DataTypes.STRING,
      unique: true
    },
    password: DataTypes.STRING
  }, 
// {
//     hooks: {
//       beforeCreate: hashPassword,
//       beforeUpdate: hashPassword,
//       beforeSave: hashPassword
//     }
//   }
)

  User.prototype.comparePassword = function (password) {
    return bcrypt.compareAsync(password, this.password)
  }

  User.associate = function (models) {
  }

  return User
}

```

`新建config/config.js`:数据库相关配置
```
var config = {
  host     : 'localhost', // 主机名
  username : 'root', // 用户名
  password : '1234567w', // 口令
  database : 'nodemysql', // 使用哪个数据库
  port: 3000, // 端口号，MySQL默认3306
  authentication: {
    jwtSecret: process.env.JWT_SECRET || 'secret'
  }
};
module.exports = config;
```
`修改router/index.js`:添加注册接口
```
// // index.js
const express = require('express');
const router = express.Router();
const AuthenticationControllerPolicy = require('../policies/AuthenticationControllerPolicy')
const AuthenticationController = require('../controllers/AuthenticationController')

router.post('/register',
    AuthenticationControllerPolicy.register,
    AuthenticationController.register)
/* GET home page. */
router.post('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});
module.exports = router;
```
`新建policies/AuthenticationControllerPolicy.js`:用于校验注册逻辑
```
const Joi = require('@hapi/joi');
// 到处方法的命名和使用方法完全相同
module.exports = {
  register (req, res, next) {
    const schema = Joi.object({
      email: Joi.string().email(),
      password: Joi.string().regex(new RegExp('^[a-zA-Z0-9]{8,32}$'))
    });
    const{error} = schema.validate(req.body);
    if(error) {
      switch(error.details[0].context.key) {
        case'email':
          res.status(400).send({
            error: 'You must provide a valid email address'
          });
          break;
        case'password':
          res.status(400).send({
            error: `The password provided failed to match the following rules:
              <br>
              1. It must contain ONLY the following characters: lower case, upper case, numerics.
              <br>
              2. It must be at least 8 characters in length and not greater than 32 characters in length.
            `
          });
          break;
        default:
          res.status(400).send({
            error: 'Invalid registration information'
          });
      }
    }else{
      next();
    }
  }
};

```
`新建controllers/AuthenticationController.js`:用于在数据库表中创建注册数据逻辑
```
const {User} = require('../models')
const jwt = require('jsonwebtoken')
const config = require('../config/config')

function jwtSignUser (user) {
  const ONE_WEEK = 60 * 60 * 24 * 7
  return jwt.sign(user, config.authentication.jwtSecret, {
    expiresIn: ONE_WEEK
  })
}

module.exports = {
  async register (req, res) {
    console.log(14, req.body)
    try {
      const user = await User.create(req.body)
      console.log(user)
      const userJson = user.toJSON()
      res.send({
        user: userJson,
        token: jwtSignUser(userJson)
      })
    } catch (err) {
      res.status(400).send({
        error: 'This email account is already in use.'
      })
    }
  }
}

```
**`5.3 接口测试`**

>此处推荐一款接口测试工具！`POSTMAN`！ 简单来说，四个词，简单实用大方美观！


* 打开`postman`
用post方法请求接口，可以看到下面可以成功的返回message
缺图
缺图
缺图
缺图
缺图
缺图


按照相关提示进行 注册数据，即可

`打开数据库可视化工具：`
可以看到在table表中有一个名为user的数据表,在user表中可以看到数据创建成功:
缺图缺图缺图缺图缺图缺图缺图缺图


我们的注册接口就成功啦！

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

你可能需要：
* [NO2:Node JS + MySQL CRUD Workshop : Insert, Update, Select, Delete](https://www.jianshu.com/p/02085294b6ed)
* [使用 Node.js + Express 开发服务端](https://www.jianshu.com/p/96df1d7bef0b)
扩展： 
[node.js url.parse 的解析](https://www.jianshu.com/p/2ef53b77e80f)
[nodejs解析url](https://cloud.tencent.com/developer/article/1653911)


* [使用Sequelize](https://www.liaoxuefeng.com/wiki/1022910821149312/1101571555324224)
* [sequelize](https://sequelize.org/)
* [https://github.com/caiya/node-sequelize/tree/master/bin](https://github.com/caiya/node-sequelize/tree/master/bin)

扩展
1. 
```
Access denied for user 'root'@'localhost' (using password: YES)
```
将password改为'';
2. 
```
Cannot find module 'bcrypt'
```
执行如下命令
```
npm install node-gyp -g
npm install bcrypt -g

npm install bcrypt --save
```
