---
title: 使用Node.js创建一个密码生成器
date: 2018-08-01 10:00:00
tags: Es6
---

<meta name="referrer" content="no-referrer"/>

## 一、 准备工作

**`1.1 创建项目`** 
```
$ npm init 
```
**`1.2 安装依赖`** 
```
$ npm i commander chalk clipboardy
```
**`1.3 创建入口文件index.js`** 

* 举个🌰：来了解`process.argv`
```
// index.js
console.log(process.argv)
```
终端执行命令
```
$ node index
```
在终端可以看到
> process.argv 属性返回数组，其中包含启动 Node.js 进程时传入的命令行参数。 第一个元素将是 process.execPath。 第二个元素将是正在执行的 JavaScript 文件的路径。 其余元素将是任何其他命令行参数.

执行命令
```
$ node index generate
```
第三个参数：generate


## 二、 编写命令行
**`2.1 添加版本和描述`** 
```
// index.js
const program = require('commander');
program.version('1.0.0').description('Simple password generator').parse()
```
终端执行命令：可以看到passgen的描述
![echo](https://upload-images.jianshu.io/upload_images/11846892-110a429eb9534a8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

继续执行命令：可以看到passgen的版本
![image.jpg](https://upload-images.jianshu.io/upload_images/11846892-57f09a251153ab17.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`2.2 配置密码长度命令`** 

```
const program = require('commander');

program.version('1.0.0').description('Simple password generator')
program.option('-l --length <number>', 'length of password').parse()
console.log(program.opts())
```
终端执行命令：可以看到passgen的密码长度命令

![echo](https://upload-images.jianshu.io/upload_images/11846892-acb4103ecbd9ff7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

终端执行命令:

![echo](https://upload-images.jianshu.io/upload_images/11846892-46110485e632e8fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`2.2 密码长度添加默认值：8`** 
```
program.option('-l --length <number>', 'length of password', '8').parse()
console.log(program.opts())
```
终端执行命: 不设置密码长度，可以看到使用的是默认值-8
![echo](https://upload-images.jianshu.io/upload_images/11846892-2e66db148b879ac9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

终端执行命令: 设置密码长度为10
![echo](https://upload-images.jianshu.io/upload_images/11846892-472310c65c56dbee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`2.3 配置保存密码命令`** 
```
program.option('-l --length <number>', 'length of password', '8')
.option('-s --save', 'save password to password.txt').parse()
```

![echo](https://upload-images.jianshu.io/upload_images/11846892-02df465998b44ec1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**`2.4 配置密码格式： 没有数字`** 
```
.option('-nn --no-number', 'remove numbers').parse()
```

终端执行命: 默认情况下有数字

![echo](https://upload-images.jianshu.io/upload_images/11846892-92538b5c20f136d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

终端执行命: 设置清除数字密码

![echo](https://upload-images.jianshu.io/upload_images/11846892-badabff8355a8f78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`2.5 配置密码格式： 没有符号`** 

```
.option('-ns --no-symbols', 'remove symbols').parse()
```
终端执行命: 默认情况下有符号

![echo](https://upload-images.jianshu.io/upload_images/11846892-aa9ad1459bdabce0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

终端执行命: 设置清除数字密码

![echo](https://upload-images.jianshu.io/upload_images/11846892-e93cbe2399555f17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三、 解析命令行-创建密码

```
// index.js
const program = require('commander');
const createPassword = require('./utils/createPassword')
const log = console.log

program.version('1.0.0').description('Simple password generator')
program
.option('-l --length <number>', 'length of password', '8')
.option('-s --save', 'save password to password.txt')
.option('-nn --no-numbers', 'remove numbers')
.option('-ns --no-symbols', 'remove symbols').parse()

const {length, save, numbers, symbols} = program.opts()

// Get generated password
const generatedPassword = createPassword(length, numbers, symbols)

// Output generated password

log(generatedPassword)
```
创建`utils/createPassword.js`
```
// createPassword.js
const alpha = 'qwertyuiopasdfghjklzxcvbnm'
const numbers = '0123456789'
const symbols= '!@#$%^&*_-=+'


const createPassword = (length = 8, hasNumbers = true, hasSymbols = true) => {
    let chars = alpha
    hasNumbers ? (chars += numbers): ''
    hasSymbols ? (chars += symbols): ''
    return generatePassword(length, chars)
}

const generatePassword = (length, chars) => {
    let password = ''
    for(let i = 0; i < length; i++){
        password+= chars.charAt(Math.floor(Math.random()*chars.length))
    }
    return password
}
module.exports = createPassword
```
终端执行命令：查看密码生成情况

![echo](https://upload-images.jianshu.io/upload_images/11846892-941b8805f0bfb80e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`3.1 添加color`**
```
// index.js
const chalk = require('chalk');
log(chalk.blue('Generated Password: ') + chalk.bold(generatedPassword))
```
终端执行命令：可以看到颜色有变化

![echo](https://upload-images.jianshu.io/upload_images/11846892-32d1a340cc51c1a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`3.2 添加剪贴板`**

```
// index.js
const clipboardy = require('clipboardy');
// Copy to clipboardy
clipboardy.writeSync(generatedPassword)
log(chalk.yellow('Password copied to clipboardy!'))
```

![echo](https://upload-images.jianshu.io/upload_images/11846892-c64d2dc6c8d138f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 四、 保存密码到对应的文件

```
// index.js
const savePassword = require('./utils/savePassword')
// Save to file
if (save) savePassword(generatedPassword)
```
创建`utils/savePassword.js`
```
const fs = require('fs')
const path = require('path')
const os = require('os')
const chalk = require('chalk')

const savePassword = (password) =>{
    fs.open(path.join(__dirname, '../', 'passwords.txt'), 'a', '666', (e, id) => {
        fs.write(id, password + os.EOL, null, 'utf-8', ()=>{
            fs.close(id, ()=>{
                console.log(chalk.green('Password saved to passwords.txt'))
            })
        })
    })
}

module.exports = savePassword
```

终端执行命令： 可以看到项目中生成passwords.txt文件，并且密码已经保存成功
![echo](https://upload-images.jianshu.io/upload_images/11846892-3cb3b22cd3362a8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![echo](https://upload-images.jianshu.io/upload_images/11846892-0c4fdb83241d5f64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 五、将本地npm模块配置成全局passgen

```
// package.json
  "preferGlobal": true,
  "bin":"./index.js",
```
终端执行命令:

![echo](https://upload-images.jianshu.io/upload_images/11846892-babc95043530ed78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`npm link命令：`将npm 模块链接到对应的运行项目中去，方便对本地模块进行调试和测试

```
//index.js
#!/usr/bin/env node //首行添加
```
终端执行命令：
![echo](https://upload-images.jianshu.io/upload_images/11846892-378ad30490e9f26b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：[http://nodejs.cn/api/process/process_argv.html](http://nodejs.cn/api/process/process_argv.html)

