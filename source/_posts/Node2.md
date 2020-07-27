---
title: NO2:Node JS + MySQL CRUD Workshop : Insert, Update, Select, Delete
date: 2020-07-27 17:14:25
tags:
---

你可能需要：
[NO1:使用 Node.js + Express 开发服务端](https://www.jianshu.com/p/96df1d7bef0b)
## 一、安装项目：
**`step1: install express module`**
**`step2: install express-generator module`**
**`step3: create project  myapp with command`**
**`step4: setup all file`**
**`step5: open project  in editor`**
* 安装node+express项目

并将项目views文件夹转换为ejs格式
```
$ npm install express -g
$ npm install express-generator -g
$ express --view=ejs Node //Node为项目名称
```
参考 [使用 Node.js + Express 开发服务端](https://www.jianshu.com/p/96df1d7bef0b)
* 安装mysql

参考：
[https://www.jianshu.com/p/957026a0af8c](https://www.jianshu.com/p/957026a0af8c)
[https://www.runoob.com/mysql/mysql-install.html](https://www.runoob.com/mysql/mysql-install.html)


## 二、安装相关依赖
**`step6: install mysql module`**
**`step7: install nodemon module`**
```
npm install mysql nodemon
```



项目初始化成功之后，结构如下
![image.png](https://upload-images.jianshu.io/upload_images/11846892-072cb81ef93f33d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**`step8: start server with npx nodemon`**

终端执行命令`npx nodemon`启动项目

**`step9: test server with localhost:3000`**

在浏览器中访问`http://localhost:3000`,可以看到
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8a0a17bbf47833d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**`step10: connect mysql database - create Database - create table`**
* create Database
1. 安装mysql可视化工具：[Navicat Premium](http://www.navicat.com.cn/products/)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-5fa9af6b17b1d45c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

名为`tb_book`
![image.png](https://upload-images.jianshu.io/upload_images/11846892-287f39ec13eaeca5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
2. 通过代码创建数据库+table
👇继续向下看

## 三、连接数据库 并 实现Insert, Update, Select, Delete
在`routes/index.js`据你的实际配置修改数据库用户名、及密码及数据库名：

####通过代码创建数据库
```
// index.js
var express = require('express');
var router = express.Router();
var mysql = require('mysql');
var db = mysql.createConnection({
  host     : 'localhost',
  user     : 'root',
  password : '1234567w',
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
在浏览器中访问`http://localhost:3000/createdb`,可以看到数据库创建成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-a11076e500f9b461.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在终端可以看到
![image.png](https://upload-images.jianshu.io/upload_images/11846892-e2bbf12b1267b7e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
同样打开可视化工具也可以看到`nodemysql`创建成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-34169c49fb70ab88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
---
####通过代码创建table
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
// Create table
router.get('/createpoststable', (req,res)=> {
  let sql = 'CREATE TABLE posts(id int AUTO_INCREMENT, title VARCHAR(255), body VARCHAR(255), PRIMARY KEY (id))';
  db.query(sql, (err, result) => {
    if (err) throw err;
    console.log(result)
    res.send('Posts table created...')
  })
})
...
```
在浏览器中访问`http://localhost:3000/createpoststable
`,可以看到table创建成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-9bdffb7b567a8943.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
同样打开可视化工具也可以看到`posts`创建成功
![image.png](https://upload-images.jianshu.io/upload_images/11846892-dcc66a46fedf5f63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
####通过代码向table中插入数据
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

---
####通过代码读取table中数据
1. 读取表中全部数据
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
})...
```
在浏览器中访问`http://localhost:3000/getposts`,可以看到读取数据成功！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-cfc7f96056b9c9e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

终端可以看到读取的数据：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c37d906b8b1a4715.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 根据id读取表中部分数据
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


---
####通过代码更新table中数据
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


---
####通过代码删除table中数据
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



## 四、通过模版语言展示数据库数据

**`step11: show data` **
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

再见👋。
你可能需要：

[NO3:Node.js + mysql+Sequelize实现注册接口及校验逻辑](https://www.jianshu.com/p/62d034e4ca8f)



