---
title: 云图梭桌面端electron安装&运行流程&打包流程
date: 2022-04-12 17:01:03
tags:
---

* 终端执行命令,打开镜像配置地址：
```
open .npmrc
```
* 在打开的文件中写入如下镜像地址
```
ELECTRON_BUILDER_BINARIES_MIRROR=http://npm.taobao.org/mirrors/electron-builder-binaries/
electron_mirror=https://cdn.npm.taobao.org/dist/electron/
```


* 删除项目的node_modules

```
rm -rf node_modules
npm i
cd render/ 
rm -rf node_modules
npm i
cd ../
npm run zoomInit:mac
npm run dev
```

* 打包流程

1、 登陆
2、 选择配置，添加需要打包的分支、点击应用-> 点击保存
3、 选择Build ,选择对应的分支进行打包
4、 打包完成之后，点击进入，复制链接
