---
title: 请说说npm/cnpm/yarn/pnpm的区别?
date: 2022-10-25 10:50:35
tags:  Node
---

<meta name="referrer" content="no-referrer"/>


## 前言
在前端工程化的背景下，依赖管理越来越得到重视，在开发的时候经常会接触到各种包管理工具，也会经常用到`npm`, `yarn`, `pnpm`等等指令，这篇文章主要是讲一讲这些概念之间的区别。

## 一、npm

NPM（Node Package Managemnt）为Node创造了一个强大且丰富的生态，是Node.js能够如此成功的主要原因之一。npm也是node.js内置的包管理器，和node一并安装。我们可以从npm的发展历程来看其的特性变化。

### 1. npm v2
特性 ：采用简单的递归安装方法，将每个模块的依赖安装到自身的node_modules文件夹中，形成一个高度嵌套的依赖树。
可能存在的问题 ：

项目规模比较大时，容易出现重复依赖，互相依赖，嵌套地狱等问题。
大量的重复依赖安装，造成空间资源的大量浪费，同时也会造成依赖安装时间过长（体积大，安装慢）。
由于嵌套层级过深会导致文件路径过长，在windows系统中可能会引发错误。
windows 文件系统中，文件路径不能超过 260 个字符长度。

### 2. npm v3
特性 ：v3版本作了较大的更新，采取了扁平化的依赖结构。
可能存在的问题 ：

采取扁平化的结构可以解决上述的问题。但是也会存在新的问题。为了生成扁平化的依赖树，npm需要便利项目中的所有依赖关系，构建完整的依赖关系树，这是一个比较耗时的操作，所以也会造成依赖安装时间过长。这个是github上npm仓库中的一个issue对此的描述

### 3. npm v5
特性 ：引入了package-lock.json机制，保证了依赖安装的确定性。package-lock.json 的作用是锁定项目的依赖结构，理论上只要项目中存在 package-lock.json 文件，每次执行 npm install 后生成的node_modules 目录结构一定是完全相同的。
其实在package-lock.json机制出现之前，可以通过npm-shrinkwrap实现锁定依赖结构，但是npm-shrinkwrap的默认关闭的，需要主动执行。

### 什么是语义版本控制（semver）？

在了解为什么要引入锁定依赖结构的机制之前，我们需要了解npm的语义版本控制（semver）。简单来说，npm包的版本描述并不是绝对精确的，而是包含一定语义（可变化）。我们可能会遇到形如这样的版本号。

```
"@types/react": "^18.0.12",
```
这里的 ^ 表示指定的 MAJOR 版本号下, 所有更新的版本。这里会安装18.x.x的任意最新版本。

npm包的版本规范为MAJOR.MINOR.PATCH。官网原文描述请[点击](https://semver.org/)，不同的符号含义如下图所示。

![semver.png](https://upload-images.jianshu.io/upload_images/11846892-fe16b9f38bd62507.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从这里我们知道，即使是相同的package.json，每次安装的依赖并不都是完全一样的。这可能会导致一些问题。

### 为什么要引入 lockfiles？ 在npm中即为package-lock.json
因为单一的 `package.json` 不能确定唯一的依赖树。
主要原因有两点：

不同版本的npm的安装依赖的策略和算法可能是不一样的。
就是上面提到的 `semver-range version`。`npm install` 将根据 `package.json` 中的 `semver-range version` 更新依赖，如果某些依赖更新了，就可能会导致安装的依赖不同。

因此, 保证能够完整准确的还原项目依赖 就是lockfiles出现的原因。

**配置了package-lock.json，就一定会生效吗？**

**不一定。**

主要取决于npm版本以及 `package-lock.json` 和 `package.json` 之间的兼容关系。

|  版本  |  方案 | 
|  ----  | ----  | 
|  NPM v5.0.x |  依据 package-lock.json 安装依赖
|  NPM v5.1.0 - v5.4.2 |  如果package.json中声明的依赖版本规范有符合的更新的版本的时候，会忽略package-lock.json，按照package.json 安装依赖，并更新 package-lock.json
|  NPM >v5.4.2 |  如果package.json中声明的依赖版本规范和package.lock.json中声明的依赖版本兼容，则依据package-lock.json 安装依赖；如果不兼容，按照package.json 安装依赖，并更新 package-lock.json

## 二、yarn

yarn的出现解决了npm存在的一些比较严重的问题，主要是依赖的`确定性`，`完整性`，`依赖安装速度`等等。
这里要注意的是，yarn的出现是在2016年，此时 npm 处于 v3 时期，之后npm的更新也在不断实现yarn所拥有的一部分优点。这里所说的yarn的优点主要是针对早期的npm而言。

### 1. yarn的优点

* 速度快 yarn的速度快是其特性共同表现出来的优点。
* 依赖的确定性 通过yarn.lock等机制，可以锁定依赖的版本，保证相同的依赖关系所安装的依赖是一致的。（npm v5中提出了package-lock.json实现相同的功能）
* 扁平化依赖结构，减少依赖的冗余 yarn实现了扁平化依赖结构，避免了相同的依赖被重复安装，减小体积，加快安装速度。（npm v3中也做了相同的优化）
* 网络性能更好 采用了请求排队的理念,类似于并发池连接,利用并行下载以最大化网络资源利用率;同时也引入了一种安装失败的重试机制。
* 实现了离线模式 通过缓存机制，实现了离线模式。可以让 Yarn 在网络出现故障的情况下仍然能正常工作。（npm也具有缓存机制）

## 三、pnpm

pnpm是一个比较新的包管理工具，虽然比较新，但是热度还是挺高的。 这个是pnpm[官网](https://www.pnpm.cn/motivation)的描述。

### 1. pnpm的优点
* 速度非常快，超过了npm和yarn
* pnpm继承了yarn的所有优点，包括离线模式和确定性安装。

### 2. pnpm的不同点
* 利用硬链接和符号链接来避免复制所有本地缓存源文件，从而节省磁盘空间并提升安装速度。

* 非扁平的 node_modules 目录。

简单点来说，

npm和yarn实现的都是`项目级别`的依赖去重，项目和项目之间可能还具有大量相同的依赖。100个项目就对应100份依赖文件，这些依赖可能有大量的重复依赖，从而占用了大量的硬盘资源。
pnpm实现的是`机器级别`的依赖去重，所有文件都保存在硬盘上的统一的位置。通过硬连接，项目之间方便地共享相同版本的依赖包。
以项目和依赖包的比例来看，节省了大量的硬盘空间， 并且安装速度也大大提高了！


## 四、npm, pnpm, yarn 比较

|  特性  |  npm 2.X | npm 3.X | npm 5.X | yarn | pnpm|
|  ----  | ----  | ----  | ----  | ----  | ----  | 
|  速度|  慢 | 慢 | 较快  | 快  | 非常快  |
| 依赖共享 | 否 | 否 | 否 | 否 | 是 |
| 嵌套化依赖| ✅ |   | | | ✅| 
| 扁平化依赖| | | 		✅| 	✅	| ✅	| 
| 锁定依赖结构	| | | 		✅| 	✅| 	✅| 
| package-lock.json| | | 			✅| | | 		
| yarn.lock	| | | | 			✅	| | 
| 离线安装	| | | | 			✅| 	✅| 

## 扩展 npx、cnpm
npx, cnpm都不是包管理工具。

### npx

npx是npm内置的一个命令，可以用来执行二进制文件。比如之前主要是通过npm scripts来执行二进制文件，有了npx指令之后，可以直接在npx 后 加上需要执行的二进制文件。
举个栗子：
```
"scripts": {
    "serve": "vue-cli-service serve"
}
```

一般情况，我们通过 `npm run serve` 启动项目，本质上还是运行 `vue-cli-service serve`
有了npx之后，可以直接执行 `npx vue-cli-service serve`
### cnpm

cnpm跟npm用法完全一致，只是在执行命令时将npm改为cnpm。
为了解决npm服务器在国外而产生的网速慢，连接不稳定的问题，淘宝团队建立了npm在国内的镜像服务器，通过cnpm指令来连接。

> cnpm官网：“这是一个完整 npmjs.org镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。

安装的命令为：
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
当然也可以继续使用npm，把源替换成淘宝的镜像。
```
临时：npm --registry https://registry.npm.taobao.org install express
永久：npm config set registry https://registry.npm.taobao.org

```
## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [浅谈 npm, yarn, pnpm之间的区别](https://juejin.cn/post/7107902138952450061)