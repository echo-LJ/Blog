---
title: 配置sshKey
date: 2022-04-12 17:08:52
tags: SSH-keys
---

<meta name="referrer" content="no-referrer"/>

## 配置github或gitlab的sshKey

* 在`终端`或`git bash`中执行命令

**`查看本机是否已经配置过密钥`**

```
$ cd ~/.ssh
$ open ~/.ssh
$ ls -all
```
**`生成密钥`**
* 生成`id_rsa`密钥
```
$ ssh-keygen -t rsa -C "xx@163.com"
// 直接回车
```
* 生成`自定义命名`密钥
```
$ ssh-keygen -t rsa -C "xx@163.com" -f ~/.ssh/id_rsa_gitlab
// 直接回车，生成名为id_rsa_gitlab的密钥
```
**`复制密钥`**
```
$ cat .ssh/id_rsa.github.pub
```
**`将SSH Key密钥添加到ssh-agent`**
```
$ eval $(ssh-agent -s)
$ ssh-add ~/.ssh/github 

```
**`测试SSH连接`**
```
$ ssh -T git@github.com
$ ssh -T git@gitlab.com
```

如图所示：则代表链接成功：

![success.png](https://upload-images.jianshu.io/upload_images/11846892-899738aa27ac2d80.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 扩展在root中生成sshKey

* 在`终端`或`git bash`中执行命令
**`生成密钥`**

```
$ sudo ssh-keygen -t rsa -C “xx@163.com”
// 对应路径为/var/root/.ssh
$ sudo cat /var/root/.ssh/id_rsa.pub
```
## 存在多个密钥，需要重定向密钥

```
$ eval $(ssh-agent -s)
$ ssh-add ~/.ssh/id_rsa.github 
```
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

### 参考链接:

* [MAC同时配置gitlab和github的sshKey](https://blog.csdn.net/MichelleZhai/article/details/123106175)

