---
title: 云图梭桌面端electron安装&运行流程
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