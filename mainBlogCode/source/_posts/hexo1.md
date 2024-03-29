---
title: 基于HEXO博客框架和Github搭建个人博客简单教程
date: 2017-01-01 10:00:00
tags: Hexo
---

<meta name="referrer" content="no-referrer"/>

### 准备工作
配置环境

* 安装Node.js
* 安装Git
* 注册GitHub

## Hexo 部分
---
### Hexo简介
Hexo 是一个轻量的静态博客框架。通过Hexo可以快速生成一个静态博客框架,仅需要几条命令就可以完成,相当方便。
而架设Hexo的环境更简单了 不需要 lnmp/lamp/XAMPP 这些繁琐复杂的环境 仅仅需要一个简单的http服务器即可使用,或者使用互联网上免费的页面托管服务

### Hexo安装方法
参考官网中文文档,只需要一条命令即可自动安装hexo框架。
```
npm install -g hexo-cli #使用 npm 安装 Hexo
```

* 初始化
创建一个文件夹，如：Blog，cd到Blog里执行hexo init的。命令：
```
mkdir Blog
cd Blog
hexo init
```
* 生成静态页面
继续再Blog目录下执行如下命令，生成静态页面
```
hexo generate （或者 hexo g）
```

* 本地启动
启动本地服务，进行文章预览调试，命令：
```
hexo server
```

* 本地测试

浏览器输入 http://localhost:4000

## Github 部分
---
* 注册github
* 新建仓库
建立与你用户名对应的仓库，仓库名必须为【your_user_name.github.io】， 固定写法 !!!
* 本地_config.yml文配置件，建立关联
我们需要_config.yml文件，来建立关联(注意冒号后面要有空格)
```
deploy:  
   type: git   
   repository: https://github.com/leopardpan/  leopardpan.github.io.git  
   branch: master
```
## 部署
---
执行如下命令才能使用git部署
```
npm install hexo-deployer-git --save
```
(我没用SSH Keys如果你用了SSH Keys的话直接在github里复制SSH的就行了，总共就两种协议，相信你懂的。)
执行配置命令：
```
hexo deploy
```
然后再浏览器中输入http://username.github.io/ 就行了，把 `username` ,改成你 github 的账户名就行了

* 发布文章
每次部署的步骤，可按以下三步来进行。
```
hexo clean  
hexo generate  
hexo deploy
```
一些常用命令：
```
hexo new "postName" #新建文章  
hexo new page "pageName" #新建页面  
hexo generate #生成静态页面至public目录  
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）  
hexo deploy #将.deploy目录部署到GitHub  
hexo help  #查看帮助  
hexo version  #查看Hexo的版本
```
## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [搭建个人博客，最想推荐的 10 大框架，强烈建议收藏](https://juejin.cn/post/7002911266385707022)

