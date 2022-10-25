---
title: Chrome 灯塔简介
date: 2021-06-28 16:10:08
tags: 性能优化
---

<meta name="referrer" content="no-referrer"/>
![0_ya6nIPxZPogtSBxt.png](https://upload-images.jianshu.io/upload_images/11846892-3810af11af79abf4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`Chrome Lighthouse` 已经存在有一段时间了，但是如果我让你解释它的作用你能解释得清晰吗？

我发现很多 Web 开发人员，包括初学者，都没有听说过这个工具，而那些已经尝试过，还没有尝试过的人，这并不酷。

在本文中，我将向您介绍 `Chrome Lighthouse`，它的作用以及如何开始使用它。

让我们开始吧：

>根据维基百科，**lighthouse**是一座塔楼、建筑物或其他类型的结构，它用灯和镜头系统发出光线，作为海上或内陆水道船舶的导航设备。

好的，让我们把它变成一个技术术语;

>灯塔是塔，建筑物，或设计为发射其他类型的结构, 它在Chrome开发者工具或面板中作为开发人员的开发指南。


有道理吗？

好吧，`Lighthouse` 是由 Google 开发的一种工具，用于分析网络应用程序和网页，收集现代性能指标和对开发人员最佳实践的见解。

我们可以将 `Lighthouse` 当作汽车中用于检查和平衡汽车速度限制的速度表。

基本上，`Lighthouse` 与开发人员最佳实践和性能指标一起工作。它会在 Web 应用程序上运行检查，并为您提供有关错误、低于标准的做法、提高性能的提示以及如何修复它们的反馈。

根据 Google Developers Docs 上的描述

>Lighthouse 是一种用于提高网页质量的开源自动化工具。您可以针对任何网页、公共网页或需要身份验证的网页运行它。它对性能、可访问性、渐进式 Web 应用程序等进行了审核。

>您可以在 Chrome DevTools 中、从命令行或作为 Node 模块运行 Lighthouse。您为 Lighthouse 提供一个 URL 进行审核，它会针对页面运行一系列审核，然后生成关于页面表现如何的报告。从那里，使用失败的审计作为如何改进页面的指标。每个审计都有一个参考文档，解释为什么审计很重要，以及如何解决它。

这几乎就是 Lighthouse 的全部内容，它会审核 Web 应用程序的 URL，并根据 Web 标准和开发人员最佳实践生成一份报告，告诉您 Web 应用程序的好坏程度。报告的每个部分还附有文档，解释了为什么对应用的该部分进行审核、为什么应该改进应用的该部分以及如何修复它。

以下是此博客https://bolajiayodeji.com的lighthouse审核报告演示

![L：我的博客  R：灯塔审计报告](https://upload-images.jianshu.io/upload_images/11846892-9fc98b587f410325.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

很酷吧

你应该注意的是，我在第一次审计时没有得到这样的好分数。我不得不使用我的第一份报告来修复和改进我的应用程序的性能和质量。

这就是 Lighthouse 背后的理念，它是为了识别和修复影响您网站性能、可访问性和用户体验的常见问题。

![](https://upload-images.jianshu.io/upload_images/11846892-ab69e8d39730ae0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在，让我们进入有趣的部分，如何开始使用它！

`LightHouse` 可在三个工作流程中使用

* Chrome 开发者工具
* 命令行（Node.js）
* Chrome 扩展程序

我个人更喜欢在开发工具中使用 LightHouse。使用扩展程序没有意义，因为开发工具和扩展程序在同一个 chrome 浏览器中工作，我们的偏好各不相同，使用最适合你的。

Lighthouse 在添加到 Chrome DevTools 之前首先只能使用 Chrome 扩展程序。

在命令行上使用 lighthouse 也很酷，（对于极客来说）

让我们开始吧！！！

---

## 1. 在 Chrome DevTools 中运行 Lighthouse


* 下载[谷歌浏览器](https://www.google.com/chrome/)

> Lighthouse 只能在电脑端运行，不能在移动端运行。

* 转到您要在 Google Chrome 中审核的 URL。

>你可以审核网络上的任何 URL。

* 打开 Chrome 开发者工具

```
Command+Option+C (Mac)
Control+Shift+C (Windows, Linux, Chrome OS).
```

* 单击审核面板

![左边是将被审计的页面的视口，这是我的博客,右侧是Chrome DevTools 的 Audits面板，现在由 Lighthouse 提供支持](https://upload-images.jianshu.io/upload_images/11846892-0bbb64e2a398a624.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 点击 Run audits

>DevTools 向您显示审计类别列表。确保您将它们全部选中。如果您想个性化要审核的应用程序部分，可以通过检查要审核的类别进行设置。

* 60 到 90 秒后 - 根据您的网络速度，Lighthouse 会在页面上为您提供报告。

> 并非只有网速和预安装的扩展可能会影响lighthouse审核。为了获得更好的体验，请在 无痕模式 下进行审核来避免所有的干扰

## 2. 在命令行中运行 Lighthouse

* 下载[谷歌浏览器](https://www.google.com/chrome/)
* 下载[Node.js](https://nodejs.org/en/) 如果你已经安装了它，跳过这一步！
* 安装Lighthouse

```
npm install -g lighthouse
# or use yarn:
yarn global add lighthouse
```

>该-g标志将其安装为全局模块。

* 运行您的审计

```
lighthouse <url>
```

举例：
```
lighthouse https://bolajiayodeji.com/
```

默认情况下，Lighthouse 将报告写入 HTML 文件。您可以通过传递标志来控制输出格式。

报告可以HTML或JSON格式显示

输出样本：

```
lighthouse
# saves `./<HOST>_<DATE>.report.html`

lighthouse --output json
# json output sent to stdout

lighthouse --output html --output-path ./report.html
# saves `./report.html`

# NOTE: specifying an output path with multiple formats ignores your specified extension for *ALL* formats
lighthouse --output json --output html --output-path ./myfile.json
# saves `./myfile.report.json` and `./myfile.report.html`

lighthouse --output json --output html
# saves `./<HOST>_<DATE>.report.json` and `./<HOST>_<DATE>.report.html`

lighthouse --output-path=~/mydir/foo.out --save-assets
# saves `~/mydir/foo.report.html`
# saves `~/mydir/foo-0.trace.json` and `~/mydir/foo-0.devtoolslog.json`

lighthouse --output-path=./report.json --output json
# saves `./report.json`
```

运行$ lighthouse --helpCLI 选项

## 3. 使用 Chrome 扩展程序运行 Lighthouse

正如我之前所说，DevTools 工作流是最好的，因为它提供了与 Extension 工作流相同的好处，还有无需安装的额外好处。

要使用此方法，您需要安装扩展程序，但如果您有自己的原因，这里是指南；


* 下载[谷歌浏览器](https://www.google.com/chrome/)
* 从 Chrome 网上应用店安装[Lighthouse Chrome 扩展程序](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk) 。
* 导航到您要审核的页面
* 单击Lighthouse图标。

>它应该在 Chrome 地址栏旁边。如果没有，请打开 Chrome 的主菜单（右上角的三个点）并在菜单顶部访问它。单击后，Lighthouse 菜单将展开。

* 单击生成报告。

>Lighthouse 针对打开的页面运行审核，然后打开一个带有结果报告的新选项卡。

Bingo! 你做到了～

差不多就是这样，Lighthouse 是一个很棒的工具，尤其适合初学者。

在审核 Progressive Web 应用时，它是一个非常有用的工具。

当我开始使用 Lighthouse 时，我实际上学到了很多关于优化和性能标准的知识。您很快就会成为构建具有出色性能、可访问性和用户体验的完全优化的 Web 应用程序的专家.

>Lighthouse 不是魔术，它是由人类创造的。它是开源的，欢迎对其作出你的贡献。可以通过查看 github 上的 issue 跟踪器，找到可以修复的错误，或者创建和改进审核过程。issue 跟踪器也是讨论审计指标，新审计想法或与 Lighthouse 相关的任何内容的好去处。😄

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


原文链接:
* [Introduction to Chrome Lighthouse](https://www.freecodecamp.org/news/introduction-to-chrome-lighthouse/)
