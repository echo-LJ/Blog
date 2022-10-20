---
title: 终端命令大全
date: 2022-10-20 13:42:26
tags: tool
---


<meta name="referrer" content="no-referrer"/>

## 目录操作 

|  命令名   | 功能描述  | 使用举例 |
|  ----  | ----  | ----  |
| mkdir  | 创建一个目录 | mkdir dirname
| rmdir  | 删除一个目录 | rmdir dirname
| cd  | 改变当前目录| cd dirname
| pwd  | 显示当前目录的路径名| pwd
| ls  | 显示当前目录的内容| ls -la
| dircmp  | 比较两个目录的内容| dircmp dir1 dir2
| mvdir  | 移动或重命名一个目录(dir2存在，dir1则移动为dir2的子目录，否则改名为dir2)| mvdir dir1 dir2

## 文件操作

|  命令名  |  功能描述 | 使用举例
|  ----  | ----  | ----  |
|  cat |  显示文件内容 |  cat filename
|  cat |  复制filename1内容到filename2中 | cat filename1 > filename2
|  cp |  复制文件或目录(复制file1生成file2)|  cp file1 file2
|  rm |  删除文件或目录|  rm filename
|  mv |  改变文件名或所在目录 | mv file1 file2
|  more |  分屏显示文件内容(空格键阅读下一页)| more filename
|  pg |  分页格式化显示文件内容 | pg filename
|  od |  显示非文本文件的内容 |  od -c filename
|  ln |  联接文件 |  ln -s file1 file2
|  find|  使用匹配表达式查找文件(查找.c文件)|  find . -name "*.c" -print
|  file|  显示文件类型|  file filename
|  open |  使用默认的程序打开文件|  open filename

## 时间操作

|  命令名  |  功能描述 | 使用举例
|  ----  | ----  | ----  |
|  date |  显示系统的当前日期和时间 |  date
|  cal| 显示日历|  cal 8 1996
|  time| 统计程序的执行时间| time a.out

## 进程操作

|  命令名  |  功能描述 | 使用举例
|  ----  | ----  | ----  |
|  ps |  显示进程当前状态| ps u
|  kill |  终止进程 |  kill -9 30142
|  nice |  改变待执行命令的优先级| nice cc -c *.c
|  renice| 改变已运行进程的优先级 | renice +20 32768



### 参考链接：

* [Mac 终端命令大全](https://www.jianshu.com/p/3291de46f3ff)
