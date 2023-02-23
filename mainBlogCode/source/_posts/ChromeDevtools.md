---
title: 5个Chrome DevTools
date: 2023-02-22 13:53:23
tags: tool
---

<meta name="referrer" content="no-referrer"/>

`Chrome DevTools` 是网络开发人员最好的朋友，它有很多技巧和一些不为人知的功能。

大多数开发人员都了解基础知识，例如如何检查页面上的元素，但在其表面之下隐藏着更多的功能。本文的目的是揭开一些最有用但鲜为人知的特性的神秘面纱，并向您展示它们可以为您的开发过程做些什么。


![devtools.png](https://upload-images.jianshu.io/upload_images/11846892-e15b899193c488eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 引用 HTML 元素
`Chrome DevTools` 的一个鲜为人知的功能是引用 HTML 元素的能力。这可以通过两个简单的步骤完成：

1、打开 Chrome DevTools 并导航到视图Elements。

2、右键单击要引用的元素并选择`Store as global variable`。



![devtools.png](https://upload-images.jianshu.io/upload_images/11846892-eba08383d3730832.gif?imageMogr2/auto-orient/strip)

现在您拥有了对 HTML 元素的引用，您可以在控制台中访问和检查它。

## 日志点

日志点是一种向控制台提供调试信息而无需求助于console.log(). 自从我第一次听说此功能以来，我一直在使用 `Logpoints`，它们使我能够清理我的代码库中的语句碎片console.log。

您可以通过右键单击 DevTools 的源选项卡中的任意行并指定要记录的表达式来添加新的 Logpoint。执行该行时，您将在控制台中获得它的值。
![logpoint.gif](https://upload-images.jianshu.io/upload_images/11846892-ee5fefc25319161a.gif?imageMogr2/auto-orient/strip)

## 实时表达式

实时表达式是一种显示表达式值变化的功能。这有助于跟踪复杂表达式（如动画中使用的表达式）或变化很大的表达式（例如，如果您正在遍历数组）的问题。

您需要做的就是单击`Create live expressions`按钮并指定您要监视的表达式，然后它会在更改时自动显示其值。

![live_expression.gif](https://upload-images.jianshu.io/upload_images/11846892-31e5a206e0a9161a.gif?imageMogr2/auto-orient/strip)


**`其他用途:`** 使用 Live Expressions 显示鼠标坐标

## console.trace()
`console.trace()`是一个实用程序，可让您知道对特定函数进行特定调用的位置。想象一下，您有一个内部函数在不同的地方使用并导致了问题。要根除这个问题，您必须首先找出您的代码中的哪个位置调用了它。

![console.trace client.png](https://upload-images.jianshu.io/upload_images/11846892-c4959be25244b742.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


`console.trace()`提供此信息，因此您只需将其插入要跟踪的函数中即可。

![console.trace.png](https://upload-images.jianshu.io/upload_images/11846892-8dd4acb7f1810d39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意我们的堆栈跟踪如何显示对 的调用`innerFunction`之前是对 的调用`outterFunction`。

这是解决问题并找出代码中发生故障的确切位置的好方法。

## 控制台实用程序

`Console Utilities`是 Chrome 开发工具功能不可或缺的一部分。`Console Utilities API` 包含一系列用于常见任务的便利函数。

两个非常有用的是`$()`和`$$()`代替`document.querySelector()`和`document.querySelectorAll()`。这些返回您期望的节点列表，并将结果转换为数组，以便您可以使用`map()` 和`filter()`。

![console_utilities.gif](https://upload-images.jianshu.io/upload_images/11846892-61136f524555ffa3.gif?imageMogr2/auto-orient/strip)

## 结论

这些只是一些鲜为人知的 `Chrome DevTools` 功能，可以使您的开发过程更轻松。通过一些探索，您将能够找到更多有助于简化工作流程的技巧。
---

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

## 参考链接
1、https://isamatov.com/chrome-devtools-tips/#logpoints
2、https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/console/live-expressions
