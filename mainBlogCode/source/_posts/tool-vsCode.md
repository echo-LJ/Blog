---
title: 工具-VsCode添加背景图
date: 2022-10-19 10:17:16
tags: tool
---

<meta name="referrer" content="no-referrer"/>


# 前言

开心工作，快乐编码！在VsCode中添加背景图，让每次编码都能放轻松😊！

![background.png](https://upload-images.jianshu.io/upload_images/11846892-6956eaf7649ed171.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# mac中设置过程

## 1、找到vscode

* 找到vscode，右键> 选项> 在访达中显示

![position.png](https://upload-images.jianshu.io/upload_images/11846892-ae9f76cd0691133c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 点击vscode，显示包内容

![contents.png](https://upload-images.jianshu.io/upload_images/11846892-25be372b773286c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 点击进入Contents->Resources->app->out->vs->workbench，并将背景图（如图：man.jpeg）添加到该文件夹

❗️：第一次打开里面是没有图片的，这里面的文件是只读的，需要复制后修改然后替换文件！还有就是vscode更新后，会删除里面添加的内容。

![background.png](https://upload-images.jianshu.io/upload_images/11846892-13b097edb736bd72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 用编辑器打开`workbench.desktop.main.css` 文件，添加背景图片代码

```

body {
    pointer-events: auto !important;
    background-size: 70% !important;
    opacity: 0.7 !important;
    background-position: 100% 0 !important;
    background-image: url("./man.jpeg") !important;
    content: "";
    position: absolute;
    z-index: 999;
    width: 100%;
    background-repeat: no-repeat;
  }
```

![code.png](https://upload-images.jianshu.io/upload_images/11846892-885c3aae38d811be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2、重启编辑器

重启之后，即可得到带有背景图的VsCode编辑器。

![background.png](https://upload-images.jianshu.io/upload_images/11846892-6956eaf7649ed171.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️
