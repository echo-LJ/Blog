---
title: [Typescript] 为第三方库添加声明文件 .d.ts
date: 2019-04-01 10:00:00
tags: Ts
---
<meta name="referrer" content="no-referrer"/>

#### 一、问题
在实际开发项目中就遇到了这样的问题，需要在react+Typescript项目中使用`rc-checkbox`第三方包，执行安装命令,并在项目中引用
```
$ npm install rc-checkbox // 安装
$ import RcCheckbox from 'rc-checkbox'; // 引用
```
启动项目之后，发现报错
`Could not find a declaration file for module 'rc-checkbox'. '/Users/lujing/project/echo-rui/node_modules/rc-checkbox/lib/index.js' implicitly has an 'any' type.
  Try npm install @types/rc-checkbox if it exists or add a new declaration (.d.ts) file containing declare module 'rc-checkbox';` 

按照提示安装之后发现依然报错：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-301246ad14372b71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####二、错误原因
因为在使用 Typescript 的过程中， 第三方类库并没有ts的.d.ts 类型的声明文件，所以无法在目前的项目中正常使用。如果要顺利使用这些库， 可能需要我们添加声明文件。

####三、解决方法
在ts项目中引用第三方类型库，要注意第三方类库是否含有这个库的类型声明文件，可以从 [TypeSearch](https://microsoft.github.io/TypeSearch/) 中找到并安装这些第三方库的类型声明文件。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-b0f35526751062e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图片中显示在`TypeSearch`中没有查找到`rc-checkbox`

如果没有这个库的声明文件的话，我们需要手动声明这个库。查找项目中是否有以.d.ts结尾的文件；有的话，在文件中添加代码 `declare module` "第三方类库名";没有的话可以在src目录下新建一个types目录,然后在types 目录下新建一个 index.d.ts文件然后在文件中添加代码 declare module "第三方类库名"。
```
declare module 'rc-checkbox';
```

这个bug就解决了，重启项目就可以了！

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考连接：[在typescript中import第三方类库clipboard报错](https://www.cnblogs.com/songForU/p/11041222.html)


