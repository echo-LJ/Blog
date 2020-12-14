---
title: 前端自动化构建工具 ---- Plop
date: 2020-12-14 10:32:08
tags: 前端工程化
---
<meta name="referrer" content="no-referrer"/>
[plop](https://plopjs.com/documentation/#what-is-plop-):可以通过命令行去生成、处理文件模板代码等.
使用条件参考：项目的每个模块的结构骨架都非常相似，引入模版内容相同就可以使用Plop来实现自动化了，Plop旨在根据模板文件自动化创建组件。
## 一、安装plop
* 安装到项目
```
  npm install --save-dev plop
```
* 全局安装plop（可选，建议安装方便使用）
```
npm install -g plop
```
如果没有全局安装plop，那么要在package.json中的script中增加脚本命令：
```
"scripts": {
    "p": "plop"
 },
```

#### 二、创建路由Generator

**1、** 根目录创建一个`plop-template`文件夹，并创建一个`router`目录作为路由的`generator`,并创建`generator`的`js`文件和hbs模板（[Handlebars模板语法](https://handlebarsjs.com/zh/guide/#%E4%BB%80%E4%B9%88%E6%98%AF-handlebars%EF%BC%9F)）
![image.png](https://upload-images.jianshu.io/upload_images/11846892-eaca3ce1d5a18c20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##三、 generator之plop.js编写
* !!`plop.js`即`prompt.js`文件
```
const {notEmpty} = require('../util.js');

module.exports = {
    description: 'generate a controller', //描述这个generate的作用
    prompts: [{
        type: 'input', // 问题的类型
        name: 'pathName', // 问题对应得到答案的变量名，可以在acitons中使用该变量
        message: '文件名称', // 在命令行中的问题
        validate: notEmpty('pathName')
    }],
    actions: (data) => {// 这里可以通过data获取输入的pathname
        let name = data.pathName.split('/');
        name = name[name.length - 1];
        name[0] = name[0].toLocaleUpperCase();
        const actions = [
            {
                type: 'add', // 操作类型 添加文件
                path: `app/controller/${data.pathName}.ts`, //添加的文件的路径
                templateFile: 'dev-scripts/plop-templates/router/index.hbs', //模版文件的路径
                data: {
                    name
                }
            }
        ];

        return actions;
    }
};
```
* hbs模板文件
!!模版文件作用：编写生成文件的模版内容，可自己根据项目需求进行定义。
```
require('module-alias/register');
import BaseController from '@base/baseController';
import { AController } from '@lib/aRouter';

export default class {{ name }}Controller extends BaseController {
}
```
## 四、plop使用
在项目的根目录下创建plopfile.js文件

*  设置到plopfile.js
```
const routerGenerator = require('./dev-scripts/plop-templates/router/prompt');

module.exports = function (plop) {
    plop.setGenerator('router', routerGenerator);
};
```
##五、 运行plop、生成文件
* 执行步骤一、的脚本命令
```
$ npm run p
```
![image.png](https://upload-images.jianshu.io/upload_images/11846892-7757ee07e09cb588.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 输入文件名
!!在配置的路径下生成对应的文件
![image.png](https://upload-images.jianshu.io/upload_images/11846892-f4864eb0598a116d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 生成文件即是模版的内容
![image.png](https://upload-images.jianshu.io/upload_images/11846892-65892955b9098465.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-2814dec9f30b593b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
大功告成✌️✌️✌️





