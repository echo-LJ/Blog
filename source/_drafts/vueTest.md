---
title: Vue.js 单元测试
tags:
---

## 组件单元测试的好处

* 提供描述组件行为的文档
* 节省手动测试的时间
* 减少研发新特性时产生的bug
* 改进设计
* 促进重构

自动化测试使得大团队中的开发者可以维护复杂的基础代码。

## 单元测试简介

> 单元测试（unit testing），是指对软件中的最小可测试单元进行检查和验证。

简单来说，`单元`就是人为规定的最小的被测功能模块。单元测试是在软件开发过程中要进行的最低级别的测试活动，软件的独立单元将在与程序的其他部分相隔离的情况下进行测试。

对于开发活动中的各种测试，最常见的划分方法：从下至上依次为 **单元测试->集成测试->端到端测试** ，随着其集成度的递增，对应的自动化程度递减。

端到端（在浏览器等真实场景中走通功能而把程序当成黑盒子的测试）与集成测试（集合多个测试过的单元一起测试）的反馈与修复的周期比较长、运行速度慢，测试运行不稳定，由于很多时候还要靠人工手动进行，维护成本也很高。而单元测试只针对具体一个方法或API，定位准确，采用 mock 机制，运行速度非常快（毫秒级），又是开发人员在本地执行，反馈修复及时，成本较低。

我们把绝大部分能在单元测试里覆盖的用例都放在单元测试覆盖，只有单元测试测不了的，才会通过端到端与集成测试来覆盖。

**讲解单元测试的具体概念之前，先 咀个栗子 直观了解下：**

比如我们有这样一个模块，暴露两个方法用以对菜单路径进行一些处理：

```
// src/menuChecker.js

export function getRoutePath(str) {
  let to = ""
  //...
  return to;
}

export function getHighlight(str) {
  let hl = "";
  //...
  return hl;
}
```
编写对应的测试文件：

```
import {
  getRoutePath,
  getHighlight
} from "@/menuChecker";

describe("检查菜单路径相关函数", ()=>{

  it("应该获得正确高亮值", ()=>{
    expect( getHighlight("/myworksheet/(.*)") ).toBe("myTickets");
  });

  it("应该为未知路径取得默认的高亮值", ()=>{
    expect( getHighlight("/myworksheet/ccc/aaa") ).toBe("mydefaulthl111");
  });

  it("应该补齐开头的斜杠", ()=>{
    expect( getRoutePath("/worksheet/list") ).toBe('/worksheet/list');
  });

  it("应该能修正非法的路径", ()=>{
    expect( getRoutePath("/myworksheet/(.*)") ).toBe("/myworksheet/list");
  });
});
```
![image](https://pic.xiaohuochai.site/blog/browserRender1.png)




