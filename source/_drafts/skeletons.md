---
title: skeletons
date: 2020-11-26 10:22:20
tags:
---

### 前端项目模版-开发流程

##### 1、初始化项目
```
$ npm init 
// 生成package.json
$ commitizen init cz-conventional-changelog --save --save-exact 
// 添加changelog依赖包
```
* 安装生成 Change log 的工具

```
$ npm install -g conventional-changelog-cli
```
配置脚本+执行`npm run changelog`生成changelog文件

```
"scripts": {
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -s -r 0"
  },
```
* 配置`eslint`+`prettier`
```
$ npm install --save-dev prettier eslint-plugin-prettier
```
添加`.eslintrc.js`文件
```
"lint-staged": {
    "*.{js,vue}": [
      "npm run lint:es",
      "git add"
    ],
    "*.ts": [
      "npm run lint:ts",
      "git add"
    ]
  }`
  ```
添加`.eslintignore`文件
```
// 配置语法检查忽略的文件
```
配置eslint 执行脚本
```
"lint:ts": "cd server && npm run lint",
    "lint:es": "cd client && npm run lint",
```

* 添加`.gitignore`文件

* 配置git提交规范

参考文章：https://www.jianshu.com/p/cdd749c624d9
参考文章：https://www.cnblogs.com/daysme/p/7722474.html
// 参考文章：https://segmentfault.com/a/1190000017790694
```
$ npm i --save-dev husky lint-staged
```
添加 hook 函数
```
"husky": {
  "hooks": {
    "pre-commit": "lint-staged",
    // git commit 执行这个命令，这个命令在调起 lint-staged
    // "commit-msg": "node scripts/verify-commit-msg.js"
    // 暂时不用，因为项目中使用changelog依赖
  }
}
// validate-commit-msg 用于检查 Node 项目的 Commit message 是否符合格式。
```
配置`lint-staged`
```
"lint-staged": {
    "*.{js,vue}": [
      "npm run lint:es",
      "git add"
    ],
    "*.ts": [
      "npm run lint:ts",
      "git add"
    ]
  }
```

## 创建serve项目
通过egg+ts框架
参考文档：https://eggjs.org/zh-cn/intro/quickstart.html

```
$ mkdir server
$ cd server
$ npm init egg --type=ts
$ npm i 
$
```
* server项目中使用typescript
```
$ npm install --save-dev typescript tslint
```


