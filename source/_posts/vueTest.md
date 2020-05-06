---
title: vueTest
date: 2020-05-06 15:15:23
tags: vue test
---

Author: Echo
Time: 2020-05-06 15:15:23

## 组件单元测试的好处

* 提供描述组件行为的文档
* 节省手动测试的时间
* 减少研发新特性时产生的bug
* 改进设计
* 促进重构

自动化测试使得大团队中的开发者可以维护复杂的基础代码。

##  I. 单元测试简介

---

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
运行该测试文件，得到如下输出：

![![20200506151145.png](https://upload-images.jianshu.io/upload_images/11846892-1dec9c605aeb4dcd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
](https://upload-images.jianshu.io/upload_images/11846892-e6bc2edad5dc4097.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由此，我们对一次单元测试的过程有了基本的了解。

## 断言(assertions)

断言是单元测试框架中核心的部分，断言失败会导致测试不通过，或报告错误信息。

对于常见的断言，举一些例子如下：

- 同等性断言 Equality Asserts

      1. expect(sth).toEqual(value)
      2. expect(sth).not.toEqual(value)
- 比较性断言 Comparison Asserts

      1. expect(sth).toBeGreaterThan(number)
      2. expect(sth).toBeLessThanOrEqual(number)
- 类型性断言 Type Asserts

      1. expect(sth).toBeInstanceOf(Class)
- 条件性测试 Condition Test

      1. expect(sth).toBeTruthy()
      2. expect(sth).toBeFalsy()
      3. expect(sth).toBeDefined()

## 断言库

断言库主要提供上述断言的语义化方法，用于对参与测试的值做各种各样的判断。这些语义化方法会返回测试的结果，要么成功、要么失败。常见的断言库有 Should.js, Chai.js 等。

## 测试用例 test case

为某个特殊目标而编制的一组测试输入、执行条件以及预期结果，以便测试某个程序路径或核实是否满足某个特定需求。

一般的形式为：

```
it('should ...', function() {
    ...
        
    expect(sth).toEqual(sth);
});

```

##  测试套件 test suite

通常把一组相关的测试称为一个测试套件

一般的形式为：
```
describe('test ...', function() {
    
    it('should ...', function() { ... });
    
    it('should ...', function() { ... });
    
    ...
    
});
```

## spy

> 正如 spy 字面的意思一样，我们用这种“间谍”来“监视”函数的调用情况

通过对监视的函数进行包装，可以通过它清楚的知道该函数被调用过几次、传入什么参数、返回什么结果，甚至是抛出的异常情况。

```
var spy = sinon.spy(MyComp.prototype, 'someMethod');

...

expect(spy.callCount).toEqual(1);
```

## stub
> 有时候会使用stub来嵌入或者直接替换掉一些代码，来达到隔离的目的

一个stub可以使用最少的依赖方法来模拟该单元测试。比如一个方法可能依赖另一个方法的执行，而后者对我们来说是透明的。好的做法是使用stub 对它进行隔离替换。这样就实现了更准确的单元测试。

```
var myObj = {
    prop: function() {
        return 'foo';
    }
};

sinon.stub(myObj, 'prop').callsFake(function() {
    return 'bar';
});

myObj.prop(); // 'bar'
```

## mock

`mock`一般指在测试过程中，对于某些不容易构造或者不容易获取的对象，用一个虚拟的对象来创建以便测试的测试方法

广义的讲，以上的 spy 和 stub 等，以及一些对模块的模拟，对 ajax 返回值的模拟、对 timer 的模拟，都叫做 mock 。

## 测试覆盖率(code coverage)

用于统计测试用例对代码的测试情况，生成相应的报表，比如 `istanbul` 是常见的测试覆盖率统计工具。

`istanbul` 也就是土耳其首都 “伊斯坦布尔”，这样命名是因为土耳其地毯世界闻名，而地毯是用来"覆盖"的😷。

回顾一下上面的图：


![20200506151145.png](https://upload-images.jianshu.io/upload_images/11846892-8cc2514fc4359845.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

表格中的第2列至第5列，分别对应了四个衡量维度：

* 语句覆盖率（statement coverage）：是否每个语句都执行了
* 分支覆盖率（branch coverage）：是否每个`if`代码块都执行了
* 函数覆盖率（function coverage）：是否每个函数都调用了
* 行覆盖率（line coverage）：是否每一行都执行了

测试结果根据覆盖率被分为“绿色、黄色、红色”三种，应该关注这些指标，测试越全面，就能提供更高的保证。

同时也没有必要一味追求行覆盖率，因为它会导致我们过分关注组件的内部实现细节，从而导致琐碎的测试。


## II. Vue.js 中的单元测试工具

---

## Jest

不同于"传统的"(其实也没出现几年)的 jasmine / Mocha / Chai 等前端测试框架；Jest的使用更简单（也许就是这个单词的本意“俏皮话、玩笑话”的意思），并且提供了更高的集成度、更丰富的功能。

Jest 是一个由 Facebook 开发的测试运行器，相对其他测试框架，其特点就是就是内置了常用的测试工具，比如自带断言、测试覆盖率工具，实现了开箱即用。

此外， Jest 的测试用例是并行执行的，而且只执行发生改变的文件所对应的测试，提升了测试速度。

#### 配置

Jest 号称自己是一个 “Zero configuration testing platform”，只需在 npm scripts里面配置了test: jest，即可运行npm test，自动识别并测试符合其规则的（ Vue.js 项目中一般是 __tests__ 目录下的）用例文件。

实际使用中，适当的在 package.json 的 jest 字段或独立的 jest.config.js 里自定义配置一下，会得到更适合我们的测试场景。

参考文档 https://vue-test-utils.vuejs.org/zh/guides/testing-single-file-components-with-jest.html ，可以很快在 Vue.js 项目中配置好 Jest 测试环境


#### 四个基础单词

编写单元测试的语法通常非常简单；对于jest来说，由于其内部使用了 Jasmine 2 来进行测试，故其用例语法与 Jasmine 相同。

实际上，只要先记这住四个单词，就足以应付大多数测试情况了：

* describe： 定义一个测试套件
* it：定义一个测试用例
* expect：断言的判断条件
* toEqual：断言的比较结果

```
describe('test ...', function() {
    it('should ...', function() {
        expect(sth).toEqual(sth);
        expect(sth.length).toEqual(1);
        expect(sth > oth).toEqual(true);
    });
});
```

##  Vue Test Utils

> Vue Test Utils 是 Vue.js 官方的单元测试实用工具库

它模拟了一部分类似 jQuery 的 API，非常直观并且易于使用和学习，提供了一些接口和几个方法来减少测试的样板代码，方便判断、操纵和遍历 Vue Component 的输出，并且减少了测试代码和实现代码之间的耦合。

一般使用其 mount() 或 shallowMount() 方法，将目标组件转化为一个 Wrapper 对象，并在测试中调用其各种方法，例如：
```
import { mount } from '@vue/test-utils'
import Foo from './Foo.vue'

describe('Foo', () => {
  it('renders a div', () => {
    const wrapper = mount(Foo)
    expect(wrapper.contains('div')).toBe(true)
  })
})
```

## V. 将单元测试整合到工作流中

---

写好的单元测试，如果仅仅要靠每次 npm test 手动执行，必然会有日久忘记、逐渐过时，最后甚至无法执行的情况。

有多个时间点可以作为选择，插入自动执行单元测试 -- 例如每次保存文件、每次执行 build 等；此处我们选择了一种很简单的配置办法：

首先在项目中安装 pre-commit 依赖包；然后在 package.json 中配置 npm scripts ：

```
"scripts": {
  ...
  "test": "jest"
},
"pre-commit": [
  "test"
],
```
这样在每次 git commit 之前，项目中存在的单元测试就会自动执行一次，往往就避免了 “改一个 bug，送十个新 bug” 的窘况。

## VI. 总结

---
单元测试作为一种经典的开发和重构手段，在软件开发领域被广泛认可和采用；前端领域也逐渐积累起了丰富的测试框架和方法。

单元测试可以为我们的开发和维护提供基础保障，使我们在思路清晰、心中有底的情况下完成对代码的搭建和重构。

封装好则测试易，反之不恰当的封装让测试变得困难。

可测试性是一个检验组件结构良好程度的实践标准。

参考资料：
  wx公众号：云前端
  文章: 实例入门 Vue.js 单元测试
