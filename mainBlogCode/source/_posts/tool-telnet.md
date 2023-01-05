---
title: 工具-Telnet
date: 2023-01-05 16:00:35
tags: tool
---

<meta name="referrer" content="no-referrer"/>


# 简介

Telnet协议是TCP/IP协议族中的一员，是Internet远程登录服务的标准协议和主要方式。 它为用户提供了在本地计算机上完成远程主机工作的能力。 在终端使用者的电脑上使用telnet程序，用它连接到服务器。

# Mac OS 安装telnet命令

## 1、安装 homebrew

终端输入如下命令

```
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

* 这一步，输入密码之后一直回车就可以了

##  2、查看homebrew 是否安装成功

```

$ brew help
Example usage:
  brew search [TEXT|/REGEX/]
  brew info [FORMULA...]
  brew install FORMULA...
  brew update
  brew upgrade [FORMULA...]
  brew uninstall FORMULA...
  brew list [FORMULA...]

Troubleshooting:
  brew config
  brew doctor
  brew install --verbose --debug FORMULA

Contributing:
  brew create [URL [--no-fetch]]
  brew edit [FORMULA...]

Further help:
  brew commands
  brew help [COMMAND]
  man brew
  https://docs.brew.sh
```

## 3、使用 homebrew 安装telnet

```
$ brew install telnet
```
## 4、查看是否安装成功
```
$ telnet
telnet> 
```
到这一步就安装完成了

## 5、telnet使用

```
$ telnet www.baidu.com 80
Trying 115.239.210.27...
Connected to www.a.shifen.com.
Escape character is '^]'.
```

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️