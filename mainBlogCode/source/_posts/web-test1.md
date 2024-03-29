---
title: 前端面试题 ---- [1]
date: 2017-09-01 10:00:00
tags: Web
---
 
<meta name="referrer" content="no-referrer"/>

## 页面从输入URL到页面加载显示完成，这个过程中都发生了什么？
---
1.输入域名地址
2.发送至DNS服务器并获得域名对应的WEB服务器IP地址；
3.与WEB服务器建立TCP连接；
4.服务器的永久重定向响应（从 http://example.com 到 http://www.example.com）
5.浏览器跟踪重定向地址
6.服务器处理请求
7.服务器返回一个HTTP响应
8.浏览器显示 HTML
9.浏览器发送请求获取的资源（如图片、音频、视频、CSS、JS等等）
10.浏览器发送异步请求

```
DNS服务器: 域名服务器，是进行域名和与之对应的IP地址转换的服务器。
TCP: 传输控制协议，一种面向连接的、可靠的、基于字节流的传输层通信协议。
```

## 浏览器工作原理
---

1. `用户界面` － 包括地址栏、后退/前进按钮、书签目录等，也就是所看到的除了用来显示所请求页面的主窗口之外的其他部分。

2. `浏览器引擎` － 用来查询及操作渲染引擎的接口。

3. `渲染引擎` － 用来显示请求的内容，例如，如果请求内容为html，它负责解析html及css，并将解析后的结果显示出来。

4. `网络` － 用来完成网络调用，例如http请求，它具有平台无关的接口，可以在不同平台上工作。

5. `UI后端` － 用来绘制类似组合选择框及对话框等基本组件，具有不特定于某个平台的通用接口，底层使用操作系统的用户接口。

6. `JS解释器` － 用来解释执行JS代码。

7. `数据存储` － 属于持久层，浏览器需要在硬盘中保存类似cookie的各种数据，HTML5定义了web database技术，这是一种轻量级完整的客户端存储技术
![image](https://pic.xiaohuochai.site/blog/browserRender1.png)


参考链接:https://www.cnblogs.com/xiaohuochai/p/9174471.html

## 浏览器内核
---

主要分成两部分:`渲染引擎`(layout engineer或Rendering Engine)和`JS引擎`。

`渲染引擎`:负责取得网页的内容（HTML、XML、图像等等）、整理讯息（例如加入CSS等），以及计算网页的显示方式，然后会输出至显示器或打印机。浏览器的内核的不同对于网页的语法解释会有不同，所以渲染的效果也不相同。所有网页浏览器、电子邮件客户端以及其它需要编辑、显示网络内容的应用程序都需要内核。

`JS引擎`:解析和执行javascript来实现网页的动态效果。
最开始渲染引擎和JS引擎并没有区分的很明确，后来JS引擎越来越独立，内核就倾向于只指渲染引擎。

## 常见的浏览器内核有哪些？
---

Trident内核:IE，360.... (微软开发的一种排版引擎)
Gecko内核:Firefox
Presto内核:Opera(已经废弃了)
Blink内核:Opera；
Webkit内核:Safari，Chrome。
##  cookies，sessionStorage 和 localStorage 的区别？
---
`cookie`是网站为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密），数据会在浏览器和服务器间来回传递。
`sessionStorage`和`localStorage`不会自动把数据发给服务器，仅在本地保存。

##### 存储大小:
* cookie数据大小不能超过4k。
* sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到 5M 或更大。
##### 存储时间:
* localStorage 存储持久数据，浏览器关闭后数据不丢失除非主动删除数据;
* sessionStorage 数据在当前浏览器窗口关闭后自动删除.
* cookie 设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭

```
// 存储内容类型: 
sessionStorage和localStorage只能存储字符串类型
// 获取方式:
localStorage:window.localStorage;；sessionStorage:window.sessionStorage;
// WebStorage提供了一些方法，数据操作比cookie方便；
setItem (key, value) ——  保存数据，以键值对的方式储存信息。
getItem (key) ——  获取数据，将键值传入，即可获取到对应的value值。
removeItem (key) ——  删除单个数据，根据键值移除对应的信息。
clear () ——  删除所有的数据
key (index) —— 获取某个索引的key
// cookie需要自己封装接口setCookie. getCookit
```

## 页面访问cookie的限制条件
---
跨域问题，设置了HttpOnly

参考链接:https://www.cnblogs.com/hujunzheng/p/5744755.html

## 实现浏览器内多个标签页之间的通信
---

* localstorge
localstorge在一个标签页里被添加、修改或删除时，都会触发一个storage事件，通过在另一个标签页里监听storage事件，即可得到localstorge存储的值，实现不同标签页之间的通信。
```
// a:click
$("#btn").click(function(){  
  var name=$("#name").val();  
  localStorage.setItem("name", name); 
}); 

// b:watch
window.addEventListener("storage", function(event){   console.log(event.key + "=" + event.newValue);  
}); 
```
* cookie+setInterval
将要传递的信息存储在cookie中，每隔一定时间读取cookie信息，即可随时获取要传递的信息

* websocket协议
1.首先我们得了解websocket是什么？
它是一种网络通信协议
2.为什么会用到websocket?
因为http有缺陷，通信只可以由客户端发起，服务器无法主动向客户端发送消息。
但如果这时，服务器有连续变化的状态，那么就只能使用轮询的方式来访问。
轮询:每隔一段时间，就发出一个询问.
因为websocket拥有全双工(full-duplex)通信自然可以实现多个标签页之间的通信.
* html5浏览器的新特性SharedWorker

参考链接:https://segmentfault.com/a/1190000018354650

## 页面可见性（Page Visibility API） 可以有哪些用途
---
通过 visibilityState 的值检测页面
* 当前是否可见
* 打开网页的时间
* 在页面被切换到其他后台进程的时候，自动暂停音乐或视频的播放等
##  网页验证码是做什么的，是为了解决什么安全问题
---
区分用户是计算机还是人的公共全自动程序。可以防止恶意破解密码、刷票、论坛灌水；
有效防止黑客对某一个特定注册用户用特定程序暴力破解方式进行不断的登陆尝试
##  为什么利用多个域名来存储网站资源
---
1、CDN缓存更方便

2、突破浏览器并发限制

3、节约cookie带宽

4、节约主域名的连接数，优化页面响应速度

5、防止不必要的安全问题

```
CDN:是构建在网络之上的内容发布网络，依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容发布、调度等功能模块，是用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。CDN的关键技术主要是内容存储和分布技术。简单来说，CDN主要用来使用户就近获取资源
// 突破浏览器并发限制
同一时间针对同一域名下的请求有一定数量限制，超过限制数目的请求会被阻塞。大多数浏览器的并发数量都控制在6以内。有些资源的请求时间很长，因而会阻塞其他资源的请求。因此，对于一些静态资源，如果放到不同的域名下面就能实现与其他资源的并发请求。因而后来衍生了domain dash来加大并发数，但是过多的域名会使DNS解析负担加重，因此一般控制在2-4个。对于图片资源的加载，利用css sprites技术，结合background的定位在同一张图片中加载多个图片，这也是减少并发数量的一种常用方法。

```

## 一个页面上有大量的图片，加载很慢，你有哪些方法优化这些图片的加载，给用户更好的体验
---

* 图片懒加载，在页面上的未可视区域可以添加一个滚动条事件，判断图片位置与浏览器顶端的距离与页面的距离，如果前者小于后者，优先加载。
* 如果为幻灯片、相册等，可以使用图片预加载技术，将当前展示图片的前一张和后一张优先下载。
* 如果图片过大，可以使用特殊编码的图片，加载时会先加载一张压缩的特别厉害的缩略图，以提高用户体验。
* 如果图片展示区域小于图片的真实大小，则因在服务器端根据业务需要先行进行图片压缩，图片压缩后大小与展示一致。

## 以前端角度出发做好SEO（搜索引擎）需要考虑什么？
---

* 语义化html标签

* 合理的title，description,keywords

* 主要html代码放在前面

* 少用iframe，搜索引擎不会抓取iframe中的内容

* 图片加上Alt

参考链接(了解seo): https://blog.csdn.net/jnshu_it/article/details/80029988

## 前端性能优化
---

###### 一、减少请求资源大小或者次数　
1、尽量合并和压缩css和js文件。（将css文件和并为一个。将js合并为一个）
　　原因:主要是为了减少http请求次数以及减少请求资源的大小
　　打包工具:
　　webpack
　　gulp
　　grunt
.　　....
2、尽量所使用的字体图标或者SVG图标来代替传统png图
　　因为字体图标或者SVG是矢量图，代码编写出来的，放不会变形，而且渲染速度快

3、采用图片的懒加载（延迟加载）
　　目的为了，减少页面第一次加载过程中http的请求次数
　　具体步骤:
　　　　1、页面开始加载时不去发送http请求，而是放置一张占位图
　　　　2、当页面加载完时，并且图片在可视区域再去请求加载图片信息

4、能用css做的效果，不要用js做，能用原生js做的，不要轻易去使用第三方插件。
　　避免引入第三方大量的库。而自己却只是用里面的一个小功能

5、使用雪碧图或者是说图片精灵
　　把所有相对较小的资源图片，绘制在一张大图上，只需要将大图下载下来，然后利用
　　图片定位来讲小图展现在页面中（background-position:百分比，数值）

6、减少对cookie的使用（最主要的就是减少本地cookie存储内容的大小），因为客户端操作cookie的时候，这些信息总是在客户端和服务端传递。如果设置不当，每次发送请求都将会携带cookie

7、前端与后端进行数据交互时，对于多项数据尽可能基于json格式来进行传送。相对于使用xml 来说传输有这个优势
　　目的:是数据处理方便，资源偏小

8、前端与后端协商，合理使用keep-alive

9、前端与服务器协商，使用响应资源的压缩

10、避免使用iframe
　　不仅不好管控样式，而且相当于在本页面又嵌套其他页面，消耗性能会更大。因为还回去加载这个嵌套页面的资源

11、在基于ajax的get请求进行数据交互的时候，根据需求可以让其产生缓存（注意:这个
缓存不是我们常看到的304状态码，去浏览器本地取数据），这样在下一次从相同地址获取是数据
时，取得就是上一次缓存的数据。（注意:很少使用，一般都会清空。根据需求来做）

二、代码优化相关
1、在js中尽量减少闭包的使用
　　原因:使用闭包后，闭包所在的上下文不会被释放

2、减少对DOM操作，主要是减少DOM的重绘与回流（重排）
　　关于重排（回流）的分离读写:如果需要设置多个样式，把设置样式全放在一起设置，不要一条一条的设置。使用文档碎片或者字符串拼接做数据绑定（DOM的动态创建）

3、在js中避免嵌套循环和"死循环"(一旦遇到死循环，浏览器就会直接卡掉)

4、把css放在body上，把js放在body下面
　　让其先加载css（注意:这里关于优化没有多大关系）

5、减少css表达式的使用

6、css选择器解析规则所示从右往左解析的。减少元素标签作为最后一个选择对象

7、尽量将一个动画元素单独设置为一个图层（避免重绘或者回流的大小）
　　注意:图层不要过多设置，否则不但效果没有达到反而更差了

8、在js封装过程中，尽量做到低耦合高内聚，减少页面的冗余代码。

9、css中设置定位后，最好使用z-index改变盒子的层级，让盒子不在相同的平面上

10、css导入的时候尽量减少@import导入式，因为@import是同步操作，只有把对应的样式导入后，才会继续向下加兹安，而link是异步的操作

11、使用window.requestAnimationFrame(js的帧动画)代替传统的定时器动画
　　如果想使用每隔一段时间执行动画，应该避免使用setInterval，尽量使用setTimeout
　　代替setInterval定时器。因为setInterval定时器存在弊端:可能造成两个动画间隔时间
　　缩短

12、尽量减少使用递归，避免死递归。
　　解决:建议使用尾递归

13、基于script标签下载js文件时，可以使用defer或者async来异步加载

14、在事件绑定中，尽可能使用事件委托，减少循环给DOM元素绑定事件处理函数。

15、减少Flash的使用

三、存储

1、结合后端，利用浏览器的缓存技术，做一些缓存（让后端返回304，告诉浏览器去本地拉取数据）。（注意:也有弊端）可以让一些不太会改变的静态资源做缓存。比如:一些图片，js，cs

2、利用h5的新特性（localStorage、sessionStorage）做一些简单数据的存储，
　　避免向后台请求数据或者说在离线状态下做一些数据展示。

四、其他优化


1、尽量将一个动画元素单独设置为一个图层（避免重绘或者回流的大小）
　　注意:图层不要过多设置，否则不但效果没有达到反而更差了

2、页面中的是数据获取采用异步编程和延迟分批加载，使用异步加载是数据主要是为了避免浏览器失去响应。如果你使用同步，加载数据很大并且很慢
　　那么，页面会在一段时间内处于阻塞状态。目的:为了解决请求数据不耽搁渲染，提高页面的
　　渲染效率。解决方法:需要动态绑定的是数据区域先隐藏，等数据返回并且绑定后在让其显示
　　延迟分批加载类似图片懒加载。减少第一次页面加载时候的http请求次数

3、页面中出现音视频标签，我们不让页面加载的时候去加载这些资源（否则第一次加载会很慢）
　　解决方法:只需要将音视频的`preload=none`即可。
　　目的:为了等待页面加载完成时，并且音视频要播放的时候去加兹安音视频资源

## webSocket如何兼容低浏览器
---

* 基于长轮询的 XHR
* Adobe Flash Socket
* ActiveX HTMLFile (IE)
* 基于 multipart 编码发送 XHR
参考链接:https://www.cnblogs.com/pengc/p/8718380.html

## web开发中会话跟踪的方法
---
1. 隐藏表单域：<input type="hidden">非常适合步需要大量数据存储的会话应用。
2. URL 重写：URL 可以在后面附加参数，和服务器的请求一起发送，这些参数为名字/值对。
3. Cookie：一个 Cookie 是一个小的，已命名数据元素。服务器使用 SET-Cookie 头标将它作为 HTTP响应的一部分传送到客户端，客户端被请求保存 Cookie 值，在对同一服务器的后续请求使用一个Cookie 头标将之返回到服务器。与其它技术比较，Cookie 的一个优点是在浏览器会话结束后，甚至在客户端计算机重启后它仍可以保留其值。
4. Session：使用 setAttribute(String str,Object obj)方法将对象捆绑到一个会话
5. IP地址

## HTTP method
---
一台服务器要与HTTP1.1兼容，只要为资源实现GET和HEAD方法即可。
* GET是最常用的方法，通常用于请求服务器发送某个资源。
* HEAD 与 GET 类似，但服务器在响应中值返回首部，不返回实体的主体部分。
* PUT 让服务器用请求的主体部分来创建一个由所请求的 URL 命名的新文档，或者，如果那个 URL 已经存在的话，就用干这个主体替代它。
* POST 起初是用来向服务器输入数据的。实际上，通常会用它来支持HTML的表单。表单中填好的数据通常会被送给服务器，然后由服务器将其发送到要去的地方。
* TRACE 会在目的服务器端发起一个环回诊断，最后一站的服务器会弹回一个TRACE响应并在响应主体中携带它收到的原始请求报文。TRACE 方法主要用于诊断，用于验证请求是否如愿穿过了请求/响应链。
* OPTIONS 方法请求 web 服务器告知其支持的各种功能。可以查询服务器支持哪些方法或者对某些特殊资源支持哪些方法。
* DELETE 请求服务器删除请求 URL 指定的资源

## HTTP状态码及其含义
---

| 状态码        | 类别    |  原因短语  |
| --------   | -----:  | :----: |
| 1XX        | Information（信息性状态码）      |   接收的请求正在处理    |
| 2XX        | Success（成功状态码）      |   请求正常处理完毕    |
| 3XX        | Redirection（重定向状态码）      |   需要进行附加的操作以完成请求    |
| 4XX        | Client Error（客户端错误状态码）      |   服务器无法处理请求    |
| 5XX        | Server Error（服务端错误状态码）      |   服务器处理请求出错    |

* 204	 服务器成功处理，但未返回内容。
* 304	Not Modified 未修改。	所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。客户端通常会缓存访问过的资源，通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源
* 400	Bad Request	客户端请求的语法错误，服务器无法理解
* 403	Forbidden	服务器理解请求客户端的请求，但是拒绝执行此请求
* 404	Not Found	服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面
## 什么是同源：协议相同 域名相同 端口相同
---

同源政策的目的，是为了保证用户信息的安全，防止恶意的网站窃取数据。

如果非同源，共有三种行为受到限制
1.Cookie、LocalStorage 和 IndexDB 无法读取。
2.DOM 无法获得。
3.AJAX 请求不能发送。

## 爬虫和反爬虫的理解
---

参考链接: https://www.cnblogs.com/tulintao/p/11614577.html

* `爬虫`：为了获得任何信息而采用任何手段对某一网站进行`攻击`行为，之所以成为攻击行为，因为对网站的爬虫过程会对网站造成不同程度的影响。
  爬虫与手动点击访问网站数据的主要区别在于这个是批量操作。
* `反爬虫`： 网站为了维护自己的核心安全而采取的抑制爬虫的手段。

常见的反爬虫机制：
1. 基于User-Agent反爬
* 思想： 服务器后台访问的User-Agent进行同籍，单位时间内同一个User-Agent访问的次数超过特定的阈值，就会被不同程度的封禁Ip,从而无法进行爬虫。
* 解决： 
 方案一：
   将常见的User-Agent封装到一个文件中，别人总结好的常用的User-Agent链接：https://www.cnblogs.com/zrmw/p/9332801.html
   在爬取的过程中首先导入这个文件，然后随机选择这里面的某一个User-Agent
 方案二：
   在python中使用第三方类库：fake_useragent ，生成随机的User-Agent
2. 基于IP反爬 
* 思想：后端服务器对访问进行统计，单位时间内同一个IP访问的次数超过特定的阈值，就会被不同程度的封禁Ip,从而无法进行爬虫。
* 解决
  使用不同的IP进行访问，设置一定的访问停滞，random.sleep(3)
3. 动态页面抓包 

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️