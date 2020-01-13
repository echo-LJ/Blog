---
title: echoui项目的使用发发
tags:
---


#### 替换icon图标
1. 在iconFont[https://www.iconfont.cn/?spm=a313x.7781069.1998910419.d4d0a486a]中选择echo-ui项目下的所有图标，下载文件
2. 进入echoui项目找到lib>theme-chalk>fonts文件将对应的文件替换成新文件。
3. 将下载的css文件内容更改名字index.css替换掉lib>theme-chalk>index.css
4. 进入到index.css将@font-face中url改为fonts/iconfont
5. 进入到index.css将.iconfont
```
[class^="echo-icon-"],
[class*=" echo-icon-"]
```
6. 将所有图标的命名改为例如

```
.echo-icon-spinner2
```
