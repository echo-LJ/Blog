---
title: Commit message 代码提交规范
tags:
---

Author: Echo
Time: 2019-08-21 15:36:08

#### 前言

在多人协作项目中，如果代码风格统一、代码提交信息的说明准确，那么在后期协作以及Bug处理时会更加方便。Git 每次提交代码，都要写 Commit message（提交说明），否则就不允许提交。一般来说，commit message 应该清晰明了，说明本次提交的目的。

##### Commit message 的作用
* 提供更多的历史信息，方便快速浏览
* 过滤某些commit（比如文档改动），便于快速查找信息
* 直接从commit生成Change log
* 可读性好，清晰，不必深入看代码即可了解当前commit的作用。
* 为 Code Reviewing（代码审查）做准备
* 方便跟踪工程历史
* 提高项目的整体质量，提高个人工程素质

##### Commit message 的格式

Commit message 包括三个部分：Header，Body 和 Footer

###### 一、Header
`
Header部分只有一行，包括三个字段：type（必需）、scope（可选）和subject（必需）
`

* type

 type用于说明 commit 的类别，只允许使用下面的标识

    feat：新增功能（feature）
    fix：修补bug
    docs：仅仅修改了文档，比如 README, CHANGELOG, CONTRIBUTE等等
    style： 仅仅修改了空格、格式缩进、逗号等等，不改变代码逻辑
    refactor：重构（即不是新增功能，也不是修改bug的代码变动）
    test：增加测试，包括单元测试、集成测试等
    chore：构建过程或辅助工具的变动
    type：代表某次提交的类型，比如是修复一个bug还是增加一个新的feature。
    perf: 优化相关，比如提升性能、体验
    revert: 回滚到上一个版本
    ci：自动化流程配置修改
注：如果type为feat和fix，则该 commit 将肯定出现在 Change log 之中

* scope

scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不  同。

* subject

①subject是 commit 目的的简短描述，不超过50个字符。
②以动词开头，使用第一人称现在时，比如change，而不是changed或changes
③第一个字母小写
④结尾不加句号（.）

###### 一、Body

`
Body 部分是对本次 commit 的详细描述，可以分成多行
`

###### 三、Footer
Footer 部分只用于两种情况：
* 不兼容变动
如果当前代码与上一个版本不兼容，则 Footer 部分以BREAKING CHANGE开头，后面是对变动的描述、以及变动理由和迁移方法
* 关闭 Issue

```
如果当前 commit 针对某个issue，那么可以在 Footer 部分关闭这个 issue

Closes #234

也可以一次关闭多个 issue

Closes #123, #245, #992
```

#### 全局安装
```
$ npm install -g commitizen
```
#### 项目中安装

```
$ commitizen init cz-conventional-changelog --save --save-exact
```
注意：如果是第二次配置，需要用–force：
```
$ commitizen init cz-conventional-changelog --save --force
```

#### 将未暂存文件所有变化提交到暂存区

```
$ git add .
```

① git add . ：他会监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件。

②git add -u ：他仅监控已经被add的文件（即tracked file），他会将被修改的文件提交到暂存区（git add --update的缩写）。add -u 不会提交新文件。

③git add -a ：是上面两个功能的合集（git add --all的缩写）

#### 命令行输入提交命令
```
$ git cz
```
输入命令后依次提示：
```
1. 上、下键选择要提交的更改类型

2. 此更改的范围是什么(例如组件或文件名)?(按回车键跳过)

3. 写一个简短的祈使句来描述这个变化

4. 提供更详细的更改说明:(按回车键跳过)

5. 有什么重大变化吗?

6. 这一变化是否会影响
任何未解决的问题?
```
#### 再推送到本地git仓库

```
$ git push
```

#### 打印日志命令
```
$ git log
```
#### 输出CHANGELOG记录，（文件名称自己设置），通过以下命令，在项目中生成 CHANGELOG.md 文件

* 安装生成 Change log 的工具

```
$ npm install -g conventional-changelog-cli
```
* 通过提交记录生成 CHANGELOG.md

```
$ conventional-changelog -p -i CHANGELOG.md -s
```
#### 打印出 git log 的日志记录（详细日志记录）

```
$ git log > 文件名
```

参考链接： https://segmentfault.com/a/1190000019579621