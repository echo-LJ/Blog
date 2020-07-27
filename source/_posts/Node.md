---
title: NO3:Node.js + mysql+Sequelize实现注册接口及校验逻辑
date: 2020-07-27 17:12:00
tags: 
---

在上一篇的基础上，继续升级!

[NO2:Node JS + MySQL CRUD Workshop : Insert, Update, Select, Delete](https://www.jianshu.com/p/02085294b6ed)

# 1、安装依赖
* 安装`mysql2`+`sequelize` +  `jsonwebtoken` + `joi`

* `sequelize`
`sequelize.js` 提供对 [MySQL](http://www.oschina.net/p/mysql)，[MariaDB](http://www.oschina.net/p/mariadb)，[SQLite](http://www.oschina.net/p/sqlite) 和 [PostgreSQL](http://www.oschina.net/p/postgresql) 数据库的简单访问，通过映射数据库条目到对象，或者对象到数据库条目。简而言之，就是 ORM（Object-Relational-Mapper）。Sequelize.js 完全是使用 JavaScript 编写，适用于 Node.js 的环境。
参考链接：
* [使用Sequelize](https://www.liaoxuefeng.com/wiki/1022910821149312/1101571555324224)
* [sequelize](https://sequelize.org/)
* [https://github.com/caiya/node-sequelize/tree/master/bin](https://github.com/caiya/node-sequelize/tree/master/bin)
```
 $ npm install --save sequelize mysql2 joi jsonwebtoken
```


# 2、添加+修改配置文件
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
    const model = sequelize.import(path.join(__dirname, file))
    db[model.name] = model
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
const Joi = require('joi')

module.exports = {
  register (req, res, next) {
    const schema = {
      email: Joi.string().email(),
      password: Joi.string().regex(
        new RegExp('^[a-zA-Z0-9]{8,32}$')
      )
    }
    
    const {error} = Joi.validate(req.body, schema)
    if (error) {
      switch (error.details[0].context.key) {
        case 'email':
          res.status(400).send({
            error: 'You must provide a valid email address'
          })
          break
        case 'password':
          res.status(400).send({
            error: `The password provided failed to match the following rules:
              <br>
              1. It must contain ONLY the following characters: lower case, upper case, numerics.
              <br>
              2. It must be at least 8 characters in length and not greater than 32 characters in length.
            `
          })
          break
        default:
          res.status(400).send({
            error: 'Invalid registration information'
          })
      }
    } else {
      next()
    }
  }
}

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
###3. 接口测试
>此处推荐一款接口测试工具！`POSTMAN`！ 简单来说，四个词，简单实用大方美观！

* 打开`postman`
用post方法请求接口，可以看到下面可以成功的返回message
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c7be54c38a8b1669.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

按照相关提示进行 注册数据，即可

`打开数据库可视化工具：`
可以看到在table表中有一个名为user的数据表,在user表中可以看到数据创建成功:
![image.png](https://upload-images.jianshu.io/upload_images/11846892-bb8daab7b371cb5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


我们的注册接口就成功啦！
