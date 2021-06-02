---
title: TypeScript+Rollup快速构建完整配置并发布npm包
date: 2021-06-02 10:04:47
tags: npm
---

<meta name="referrer" content="no-referrer"/>

#### 介绍
**`typescript-library-starter`**
它是一个开源的 TypeScript 开发基础库的脚手架工具，可以帮助我们快速初始化一个 TypeScript 项目，这是它的官网地址，有兴趣的同学可以了解下。

缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接缺少链接

## 初始化项目
---
```
git clone https://github.com/alexjoverm/typescript-library-starter.git typeScript-sdk-demo
cd typeScript-sdk-demo

npm install
```

## 文件目录介绍
---
安装依赖结束后，打开项目工程目录，TypeScript library starter 生成的目录结构如下：
```
├── CONTRIBUTING.md
├── LICENSE 
├── README.md
├── code-of-conduct.md
├── node_modules
├── package-lock.json
├── package.json
├── rollup.config.ts    // rollup 配置文件
├── src                 // 源码目录
├── test                // 测试目录
├── tools               // 发布到 GitHub pages 以及 发布到 npm 的一些配置脚本工具
├── tsconfig.json       // TypeScript 编译配置文件
└── tslint.json         // TypeScript lint 文件
```

## 工具集成
---
使用 TypeScript library starter 创建的项目集成了很多优秀的开源工具：
* 使用 `RollupJS` 帮助我们打包。
* 使用 `Prettier` 和 `TSLint` 帮助我们格式化代码以及保证代码风格一致性。
* 使用 `TypeDoc` 帮助我们自动生成文档并部署到 GitHub pages。
* 使用 `Jest` 帮助我们做单元测试。
* 使用 `Commitizen` 帮助我们生成规范化的提交注释。
* 使用 `Semantic release` 帮助我们管理版本和发布。
* 使用 `husky` 帮助我们更简单地使用 git hooks。
* 使用 `Conventional changelog` 帮助我们通过代码提交信息自动生成 change log。

## 关联远程git仓库
---
当前创建好的代码库与 github 远程仓库是还未关联的，也就不能提交到远程仓库，如何关联呢？
```
$ git remote add origin 仓库地址
```
仓库地址 为之前新建的仓库，查看如下：

缺图

接下来查看是否关联成功：
```
$ git remote -v
```

## 开发npm包
在我们的 src 目录下新建 index.ts 进行功能代码的编写，在此我们简单写一个 add 函数作为此次的发布的 npm 包所提供的功能。
```
src/index.ts 
function add(a: number, b: number): number {
    return a + b
}
export default add

```
但是在开发过程中往往需要开一个本地服务来测试我们的代码，也就是要写一个 demo，这时候就要用到 webpack 了。








---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接： 
* https://www.yuque.com/u188805/ewtgo7/lmvqwh?language=en-us
