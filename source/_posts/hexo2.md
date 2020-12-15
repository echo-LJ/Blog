---
title: Hexo deplay报错问题
date: 2017-01-31 10:00:00
tags: Hexo
---

<meta name="referrer" content="no-referrer"/>

今天想在自己的另一个博客地址上更新一下博客，没想到hexo deplay没有反应，以下是解决过程：

*  当部署失败的时候，请按照以下步骤进行：
报错内容如下：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-380d07b43c8f919a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 通过SSH key来上传代码
---
#### 1将SSH key添加到github中
① 打开本地文件：id_rsa.pub（文件路径可以在上一步SSH生成成功后看到路径，比如我的是c/Users/echo/.ssh/id_rsa.pub），可以将这个文件在编辑器中打开，然后全选复制。

② 登陆github，点击头像位置处 Settings ——> SSH and GPG keys ——> New SSH key，点击新建SSH key。

③ 将 ① 中复制的内容粘贴在key文本框里，title可以不用填（或者自己起一个名字也可以）
#### 2测试设置是否成功：
```
$ ssh -T git@github.com
```
如果出现如下情况
```
Hi username! You've successfully authenticated, but GitHub does not 
provide shell access.
```
将项目中如下位置文件做出更改
![image.png](https://upload-images.jianshu.io/upload_images/11846892-9dc350788418a979.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
不要使用https，而是改为SSH，如下：
```
repository: https://github.com/username/username.github.io.git
```
修改为
```
repo：git@github.com:username/username.github.io.git
```
一般这步基本就可以部署了！！！
如果部署还是失败，可以试试如下步骤（还是不行，就多试几遍。。。有点坑）
```
删除.deploy_git
$ hexo clean 
$ hexo g
$ hexo d
```
本人的问题成功解决了，希望帮到大家哈哈哈哈哈开心！

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️



