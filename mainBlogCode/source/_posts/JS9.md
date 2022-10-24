---
title: 上传图片过程中断，如何恢复传输？
date: 2022-10-24 09:37:27
tags: JS
---


<meta name="referrer" content="no-referrer"/>

## 思路方案

![截屏2022-10-24 上午9.41.04.png](https://upload-images.jianshu.io/upload_images/11846892-aa41b0bfa099e843.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 大文件上传的概念
大文件上传一般采用切片上传的方式，这样可以提高文件上传的速度，前端拿到文件流后进行切片，然后与后端进行通讯传输，一般还会结合断点继传，这时后端一般提供三个接口，第一个接口获取已经上传的切片信息，第二个接口将前端切片文件进行传输，第三个接口是将所有切片上传完成后告诉后端进行文件合并。

## 断点续传



对于大文件上传，可能会出现一些文件中断的问题，而我们为了不浪费网络资源，用户时间，可以使用断点续传的功能。

由于 featch 不能监听到传输文件的进度。所以我们还是通过 XMLHttpRequest 。

**为什么不能用 upload.onprogress？**

要恢复上传，我们就需要知道之前上传到了那里。
我们虽然可以用 `xhr.upload.onprogress` 来知道上传的进度。但是很可惜，我们不能用它来定位上传的具体进度。
因为这个方法是在数据 被发送 的时候触发的，服务器是否接收到我们并不知道，也许它是由本地网络代理缓冲的（buffered），或者可能是远程服务器进程刚刚终止而无法处理它们，亦或是它在中间丢失了，并没有到达服务器。
这就是为什么此事件仅适用于显示一个好看的进度条。
要恢复上传，我们需要 确切地 知道服务器接收的字节数。而且只有服务器能告诉我们，因此，我们将发出一个额外的请求。

![截屏2022-10-24 上午9.46.42.png](https://upload-images.jianshu.io/upload_images/11846892-7a3e93282c42f499.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 首先创建一个文件id，后面我们会用这个id去服务器查上传到哪里了。

```
$ let fileId = file.name + '-' + file.size + '-' + file.lastModified;
```

当我们改变文件名字或者大小或者修改时间，都会有一个新的id.
* 向服务器发送一个请求，询问已经有多少个字节了。

* 向服务器发送一个请求，询问已经有多少个字节了。

```
let response = await fetch('status', {
  headers: {
    'X-File-Id': fileId
  }
});

// 服务器已有的字节数
let startByte = +await response.text();
```

这假设服务器通过header的 X-File-Id 属性 跟踪文件上传。应该在服务端实现。

如果服务器上尚不存在该文件，则服务器响应应为 0。

* 然后，我们可以使用 Blob 和 slice 方法来发送从 startByte 开始的文件：

```
xhr.open("POST", "upload", true);

// 文件 id，以便服务器知道我们要恢复的是哪个文件
xhr.setRequestHeader('X-File-Id', fileId);

// 发送我们要从哪个字节开始恢复，因此服务器知道我们正在恢复
xhr.setRequestHeader('X-Start-Byte', startByte);

xhr.upload.onprogress = (e) => {
  console.log(`Uploaded ${startByte + e.loaded} of ${startByte + e.total}`);
};

// 文件可以是来自 input.files[0]，或者另一个源
xhr.send(file.slice(startByte));
```
这里我们将文件 id 作为 X-File-Id 发送给服务器，所以服务器知道我们正在上传哪个文件，并且，我们还将起始字节作为 X-Start-Byte 发送给服务器，所以服务器知道我们不是重新上传它，而是恢复其上传。
在服务器端，我们发现有一个id的文件没有上传完，并且上传的大小是 X-Start-Byte ， 继续接收新的数据附加到之前上传的文件。


### 参考链接：

* [基于js管理大文件上传以及断点续传](https://juejin.cn/post/7000654161297539079)
* [](https://juejin.cn/post/7109754397436936205)
