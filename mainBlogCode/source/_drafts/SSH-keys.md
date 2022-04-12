---
title: 配置github的ssh密钥
date: 2022-04-12 17:08:52
tags: SSH-keys
---

<meta name="referrer" content="no-referrer"/>

## 配置github的ssh密钥

在终端或git bash中执行命令

**`查看本机是否已经配置过密钥`**

```
cd ~/.ssh
open ~/.ssh
ls
```
**`生成密钥`**
```
$ ssh-keygen -t rsa -C "xx@163.com"
```
**`复制密钥`**
```
cat .ssh/id_rsa.github.pub
```
**`将SSH Key密钥添加到ssh-agent`**
```
 eval $(ssh-agent -s)
ssh-add ~/.ssh/github 

```
**`测试SSH连接`**
```
ssh -T git@github.com

```
