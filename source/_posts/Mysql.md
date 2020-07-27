---
title: macOS 上安裝 MySQL
date: 2020-07-27 17:11:18
tags:
---

> 在 macOS 下安裝 MySQL 资料库，并且基本的设定 root 密码。
## 安装步骤
* [官网页面](https://dev.mysql.com/downloads/file/?id=454017)下方` No thanks, just start my download.`
* 借用站点-[mysql-5.6.21-osx10.9-x86_64.dmg](https://cdn.ioa.tw/MacEnvInit/mysql-5.6.21-osx10.9-x86_64.dmg)
* 对` mysql-5.6.21-osx10.9-x86_64.dmg`点击两下进行安装，点击两下` mysql-5.6.21-osx10.8-x86_64.pkg` 开始安装
* ‼️若是 OSX 10.10 以上，请在 安装类型 的步骤时点击自定 把 `Startup Item` 选择 取消勾选！
* 安装完成后，打开 系统偏好设置 > MySQL > Start MySQL Server
若 系统偏好设置 里面没看到 MySQL 的话，请重开 系统偏好设置
## 变更启动权限
* 每次启动 MySQL 都要输入密码，很麻烦，所以变更一下权限，终端输入命令 `sudo chmod 777 /usr/local/mysql/support-files/mysql.server`
## 加入指令
* 打开终端输入 `vim ~/.zshrc`
* 在最下面新增 `export PATH="/usr/local/mysql/bin:$PATH"`执行`:wq`
* 退出之后，在终端执行`source ~/.zshrc`
## 设定密码
* 打开终端
* 进入 MySQL，终端执行命令 `mysql -u root`
* 执行命令`use mysql;`选择资料库
* 执行命令`update user set password=PASSWORD("你的密碼") where User='root';` 设定密码
* 执行命令`flush privileges;`刷新MySQL
* 执行命令`quit` 离开 MySQL

>注意！设置密码时，要记得加上引号，假设密码为1234，应该为：
update user set password=PASSWORD("1234") where User='root';

相关参考：[https://www.ioa.tw/macOS/MySQL.html](https://www.ioa.tw/macOS/MySQL.html)
