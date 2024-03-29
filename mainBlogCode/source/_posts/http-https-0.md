---
title: HTTP 版本
date: 2023-08-29 15:04:22
tags: HTTP
---


<meta name="referrer" content="no-referrer"/>
### HTTP 1.0(1996年)

-   任意数据类型都可以发送
-   有GET、POST、HEAD三种方法
-   无法复用TCP连接(长连接)
-   有丰富的请求响应头信息。以header中的`Last-Modified`/`If-Modified-Since`和`Expires`作为缓存标识

### HTTP 1.1(1997年)

-   引入更多的请求方法类型`PUT`、`PATCH`、`DELETE`、`OPTIONS`、`TRACE`、`CONNECT`
-   引入长连接，就是TCP连接默认不关闭，可以被多个请求复用，通过请求头`connection:keep-alive`设置
-   引入管道连接机制，可以在同一TCP连接里，`同时发送`多个请求
-   强化了缓存管理和控制`Cache-Control`、`ETag`/`If-None-Match`
-   支持分块响应，断点续传，利于大文件传输，能过请求头中的`Range`实现
-   使用了`虚拟网络`，在一台物理服务器上可以存在多个虚拟主机，并且共享一个IP地址

**缺点**：主要是连接缓慢，服务器只能按顺序响应，如果某个请求花了很长时间，就会出现请求队头阻塞

虽然出了很多优化技巧：为了增加并发请求，做域名拆分、资源合并、精灵图、资源预取...等等

最终为了推进从协议上进行优化，Google跳出来，推出`SPDY`协议

### HTTP 2.0(2015年)

说出http2中至少三个新特性？

-   使用新的`二进制协议`，不再是纯文本，避免文本歧义，缩小了请求体积
-   `多路复用`，同域名下所有通信都是在单链接(双向数据流)完成，提高连接的复用率，在拥塞控制方面有更好的能力提升
-   使用`HPACK算法将头部压缩`，用`哈夫曼编码`建立索表，传送索引大大节约了带宽
-   允许`服务端主动推送`数据给客户端
-   增加了安全性，使用HTTP 2.0，要求必须至少TLS 1.2
-   使用虚拟的流传输消息，解决了应用层的队头阻塞问题

**缺点**

-   TCP以及TCP+TLS建立连接的延时，HTTP2使用TCP协议来传输的，而如果使用HTTPS的话，还需要TLS协议进行安全传输，而使用TLS也需要一个握手过程，在传输数据之前，导致我们花掉3~4个RTT
-   TCP的队头阻塞并没有彻底解决。在HTTP2中，多个请求跑在一个TCP管道中，但当HTTP2出现丢包时，整个TCP都要开始等待重传，那么就会阻塞该TCP连接中的所有请求

### HTTP 3.0/QUIC

由于HTTP 2.0依赖于TCP，TCP有什么问题那HTTP2就会有什么问题。最主要的还是队头阻塞，在应用层的问题解决了，可是在TCP协议层的队头阻塞还没有解决。

TCP在丢包的时候会进行重传，前面有一个包没收到，就只能把后面的包放到缓冲区，应用层是无法取数据的，也就是说HTTP2的多路复用并行性对于TCP的丢失恢复机制不管用，因此丢失或重新排序的数据都会导致交互挂掉

为了解决这个问题，Google又发明了`QUIC协议`

并在2018年11月将QUIC正式改名为`HTTP 3.0`

**特点**：

-   在传输层直接干掉TCP，用`UDP`替代
-   实现了一套新的`拥塞控制算法`，彻底解决TCP中队头阻塞的问题
-   实现了类似TCP的`流量控制`、传输可靠性的功能。虽然UDP不提供可靠性的传输，但QUIC在UDP的基础之上增加了一层来保证数据可靠性传输。它提供了数据包重传、拥塞控制以及其他一些TCP中存在的特性
-   实现了`快速握手`功能。由于QUIC是基于UDP的，所以QUIC可以实现使用0-RTT或者1-RTT来建立连接，这意味着QUIC可以用最快的速度来发送和接收数据。
-   集成了TLS加密功能。目前QUIC使用的是TLS1.3


HTTPS = HTTP+ SSL/TLS

`TLS(Transport Layer Security传输层安全性)` 是 `SSL(Secure Socket Layer安全套接字层)` 的后续版本，它们是用于在互联网两台计算机之间用于`身份验证`和`加密`的一种协议

    机密性：对称加密 AES + 非对称加密 RSA
    完整性：摘要算法
    身份认证：数字证书
    https://juejin.cn/post/6844904089495535624#heading-13
    
  

### HTTP1 和 HTTP2

-   HTTP2是一个`二进制协议`，HTTP1是`超文本协议`，传输的内容都不是一样的
-   HTTP2报头压缩，可以使用HPACK进行`头部压缩`，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小
-   HTTP2`服务端推送`(Server push)，允许服务器预先将网页所需要的资源push到浏览器的内存当中
-   HTTP2遵循`多路复用`，代替同一域名下的内容，只建立一次连接，HTTP1.x不是，对域名有6~8个连接限制
-   HTTP2引入`二进制数据帧`和`流`的概念，其中帧对数据进行顺序标识，这样浏览器收到数据之后，就可以按照序列对数据进行合并，而不会出现合并后数据错乱的情况，同样是因为有了序列，服务器就可以并行的传输数据，这就是流所做的事情。HTTP2对同一域名下所有请求都是基于流的，也就是说同一域名下不管访问多少文件，只建立一次连接

## HTTP 和 HTTPS 的区别

-   HTTP是`明文传输`，不安全的，HTTPS是`加密传输`，安全的多
-   HTTP标准端口是`80`，HTTPS标准端口是`443`
-   HTTP不用认证证书`免费`，HTTPS需要认证证书`要钱`
-   `连接方式不同`，HTTP三次握手，HTTPS中TLS1.2版本7次，TLS1.3版本6次
-   HTTP在OSI网络模型中是在`应用层`，而HTTPS的TLS是在`传输层`
-   HTTP是`无状态`（每一次请求都是独立的，请求结束不会记录连接的任何信息）的，HTTPS是`有状态`（session复用）的

### 扩展知识：

##### 在发送实际请求之前自动发送一个OPTIONS预检请求？

发送OPTIONS请求作为每个请求的前奏并不是HTTP协议默认的行为。
但是在处理跨域请求时，如果需要执行预检请求，可以通过设置相应的请求头来触发浏览器自动发送OPTIONS请求。

以下是如何设置请求头以触发浏览器发送OPTIONS请求的示例：

```
// 发送跨域请求前先发送OPTIONS请求
const xhr = new XMLHttpRequest();
xhr.open('GET', 'https://example.com/resource', true);
xhr.setRequestHeader('Access-Control-Request-Method', 'GET');
xhr.setRequestHeader('Access-Control-Request-Headers', 'Content-Type');
xhr.onreadystatechange = function() {
  if (xhr.readyState === XMLHttpRequest.DONE) {
    if (xhr.status === 200) {
      // OPTIONS请求成功，执行后续操作
      // ...
    } else {
      // OPTIONS请求失败，处理错误
      // ...
    }
  }
};
xhr.send();
```

在上面的示例中，我们创建一个XMLHttpRequest对象，使用`open`方法设置请求的URL和类型（GET请求），然后通过`setRequestHeader`方法设置两个特定的请求头：`Access-Control-Request-Method`和`Access-Control-Request-Headers`。

这样设置后，当在浏览器中执行该代码时，浏览器会在发送实际请求之前自动发送一个OPTIONS预检请求。服务器端应该正确处理该预检请求并返回适当的响应。

请注意，上述代码是简化的示例，用于说明如何设置请求头来触发OPTIONS请求。在实际使用中，根据具体的场景和需求，可能需要进一步设置其他请求头和处理预检请求的逻辑。

扩展问题：
1、TCP三次握手时什么意思？
2、什么是CPU?
3、DNS域名查找的顺序

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
* [20分钟助你拿下HTTP和HTTPS，巩固你的HTTP知识体系](https://juejin.cn/post/6994629873985650696)
* [简单比较 http https http2](https://juejin.cn/post/6844903559952089102#heading-4)
* [为什么每次请求之前要发送一个OPTIONS请求](https://blog.csdn.net/luanxiyuan/article/details/100137496)

扩展知识：
* RTT（Round-Trip Time）是指一个数据包从发送到接收并返回发送方所经历的时间。它表示在网络中发送数据包到达目的地并返回的总时间。