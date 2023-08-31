---
title: 浏览器缓存-本地缓存&离线缓存
date: 2023-08-30 16:38:59
tags: HTTP
---

<meta name="referrer" content="no-referrer"/>

## 本地存储

本地存储分为`Cookie`、`LocaStorage`、`SessionStorage`、`indexDB`四种

### Cookie

最早被提出来的本地存储方式，在每一次 http 请求携带 Cookie，可以判断多个请求是不是同一个用户发起的，特点是：

* `安全问题:`如果被拦截，就可以获得 Session 所有信息，然后将 Cookie 转发就能达到目的.（[如跨站点脚本攻击（XSS）和跨站点请求伪造（CSRF）](https://juejin.cn/post/6991888178890145828)）
* `大小问题:`每个域名下的Cookie不能超过20个，大小不能超过4kb。
* Cookie在请求新页面的时候都会被发送过去
* Cookie创建成功名称就不能修改
* 跨域名不能共享Cookie。

#### 如何实现跨域名共享Cookie？
* 用 Nginx 反向代理:在同一域名下部署代理服务器，将请求转发到不同的域名，从而避免跨域问题。在代理服务器中，可以将从另一个域名获取到的Cookie传递给客户端。
1、在 Nginx 配置中添加以下内容，将请求代理到目标域名，并允许携带 Cookie：
```
location / {
  proxy_pass https://target-domain.com; // 将请求代理到目标域名
  proxy_set_header Cookie $http_cookie; // 允许携带原始请求中的 Cookie。
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_cookie_domain target-domain.com current-domain.com; //指定将目标域名的 Cookie 域名替换为当前域名，从而实现跨域名共享 Cookie。
  proxy_cookie_path / /;
}
```
2、 针对`目标域名的 HTTPS` 配置，添加以下内容，允许携带 Cookie：
```
add_header Access-Control-Allow-Origin https://current-domain.com; //头部指定允许从当前域名发送请求。
add_header Access-Control-Allow-Credentials true; //头部设置为 true，允许携带 Cookie
```

3、重新加载或重启 Nginx 以使配置生效。

这样配置后，Nginx 会将请求代理到目标域名，并传递原始请求中的 Cookie。同时，目标域名的响应中也会包含允许当前域名访问的 CORS 头部信息，从而实现了跨域名共享 Cookie。

* **同源策略**：可以通过在服务器的响应头中设置`Access-Control-Allow-Credentials`: true来启用跨域共享Cookie。同时，在发出跨域请求时，需设置`withCredentials`: true来携带Cookie信息。

* **JSONP**：JSONP是一种通过动态添加`<script>`标签来进行跨域请求的技术.在请求中，可以通过URL参数传递Cookie的值，服务器在响应时将Cookie信息嵌入到返回值中。然而，JSONP存在一些安全风险，并且只能用于GET请求。
* **子域名共享**：可以通过设置Cookie的域名为父域名来实现子域名之间的Cookie共享。例如，设置Cookie的域名为".example.com"，则所有子域名（如www.example.com、blog.example.com）都可以访问该Cookie。


#### Cookie的使用场景

* 用户认证和会话管理：在用户登录后，服务器可以生成一个包含用户认证信息的Cookie，并在之后的请求中使用该Cookie来识别用户身份，实现用户的持久登录状态和会话管理。

* 购物车和电子商务：Cookie可用于存储用户的购物车内容，以便用户在多个页面之间保持购物车状态，并在结账时获取相关信息。

* 个性化设置和偏好：网站可以使用Cookie来存储用户的个性化设置和偏好，例如语言选择、主题颜色、字体大小等，以提供更好的用户体验。

* 跟踪用户行为和分析：通过在Cookie中存储唯一标识符，网站可以跟踪用户的行为和活动，并进行分析，以了解用户的访问模式、喜好和兴趣等信息。

* 广告定向和利益推荐：广告商可以使用Cookie来跟踪用户的兴趣和互动行为，以提供更精准和个性化的广告投放，或者根据用户的兴趣推荐相关内容。

* 多域名共享数据：通过设置域名和路径来控制Cookie的访问范围，可以实现多个子域名之间的数据共享，例如在一个域名下的不同子网站之间共享用户认证状态。


#### Cookie的使用场景
* `Name`、`Size` 故名思意
* `Value`：保存用户登录状态，应该将该值加密，不能使用明文.
* `Path`：可以访问此 Cookie 的路径。比如 juejin.cn/editor ，path是/editor，只有/editor这个路径下的才可以读取 Cookie.
* `httpOnly`：表示禁止通过 JS 访问 Cookie，减少 XSS 攻击。
* `Secure(安全标志)`：只能在 https 请求中携带.
* `SameSite`：规定浏览器不能在跨域请求中携带 Cookie 减少 CSRF 攻击。"None"表示不受同源策略限制，"Lax"表示有限的同源策略（默认），"Strict"表示严格的同源策略。
* `Domain`：Cookie的作用，跨域或者 Cookie 的白名单，指定可以访问该Cookie的域名。例如，设置为".example.com"表示该Cookie可以在所有子域名下访问。，实现单点登录的话会非常有用
* `Expires/Max-size`：指定时间或秒数的过期时间，没设置的话就和 Session 一样关闭浏览器就失效.过期时间到了以后，浏览器会自动删除该Cookie。

### LocaStorage

LocalStorage是一种在Web浏览器中存储数据的持久化机制。

* 持久性： 存储是持久性的、即使关闭浏览器，数据也不会丢失，除非手动删除或过期。
* 容量限制： 大小为5MB。
* 基于域名：LocalStorage不能跨域名读取,浏览器设为隐私模式下，也不能读取 LocalStorage.
* API接口：在JavaScript中，使用localStorage对象来访问和操作LocalStorage。常用的方法包括`setItem()`用于存储数据，`getItem()`用于读取数据，`removeItem()`用于删除数据，以及`clear()`用于清空所有数据。

**LocaStorage的使用场景**: 用户的偏好设置、已选择的主题、上次访问时间等.

### SessionStorage

SessionStorage在每个会话期间存储的数据只存在于会话期间，当会话结束后，数据将被清除。

* 生命周期: SessionStorage中存储的数据仅在当前会话期间有效。会话期间是指当用户在浏览器中打开一个窗口或标签页时开始，直到关闭该窗口或标签页为止。当会话结束时，所有存储在SessionStorage中的数据都会被自动清除。
* 容量限制： 大小为5MB。
* 基于原点（Origin）: SessionStorage 和 LocalStorage 一样是在本地存储，而且都不能被爬虫爬取，并且都有同源策略的限制，只不过 SessionStorage 更加严格，只有在同一浏览器的同一窗口下才能共享。
* API接口: 同LocaStorage。

**SessionStorage的使用场景**: 网站的游客登录信息，还有临时的浏览记录。

### indexDB

是`浏览器本地数据库`，有以下特点:

* `键值对储存`: 内部用对象仓库存放数据，所有类型的数据都可以直接存入，包括js对象，以`键值对`的形式保存，每条数据都有对应的主键，主键是唯一的.
* `异步`：indexDB操作时用户依然可能进行其他操作，异步设计是为了`防止大量数据的读写，拖慢网页的表现`.
* `支持事务`：比如说修改整个表的数据，修改了一半的时候报了个错，这时候会全部恢复到没修改之关的状态，不存在修改一半成功的情况.
* `同源限制`：每一个数据库应创建它对应的域名，网页只能访问自身域名下的数据库.
* `存储空间大`：一般来说不少于`250MB`，甚至没有上限.
* `支持二进制存储`：比如ArrayBuffer对象和Blob对象.


前端存储方式除了上面四个，还有WebSQL，类似于SQLite，是真正意义上的关系型数据库，可以使用sql进行操作，只是用js时要进行转换，比较麻烦.

### 四种本地存储方式的区别

|  特点   | cookie  | SessionStorage  | LocalStorage  | indexDB  |
|  ----  | ----  | ----  | ----  | ----  |
| 存储大小  | 4kb | 5M或更大  | 5M或更大  | 无限，不小于250MB  | 
| 存储时间  | 可指定时间,没指定关闭窗口就失效 | 浏览器窗口关闭就失效  |  永久有效，除非手动删除或过期  |  永久有效
| 作用域  | 同浏览器，所有同源标签页 | 当前标签页  |  同浏览器，所有同源标签页  |   | 
| 存在于  | 请求中来回传递 | 客户端本地  |  客户端本地  | 客户端本地  | 
| 同源策略  | 同浏览器，只能被同源同路径页面访问共享 | 自己用  |  同浏览器，只能被同源页面访问共享  |   | 


## 离线存储

### Service Worker

`Service Worker`是运行js主线程之外的，在浏览器背后的独立线程，自然也`无法访问DOM`，它相当于一个代理服务器，**可以拦截用户发出的请求，修改请求或者直接向用户发出回应，不用联系服务器**。比如加载JS和图片，这就让我们可以在离线的情况下使用网络应用.

一般用于`离线缓存(提高首屏加载速度)`、`消息推送`、`网络代理`等功能。**使用Service Worker的话必须使用https协议**，因为Service Worker中涉及到请求拦截，需要https保障安全.

**用Service Worker来实现缓存分三步：**

* 注册
* 然后监听install事件后就可以缓存文件
* 下次再访问的时候就可以通过拦截请求的方式直接返回缓存的数据

```
// index.js 注册
if (navigator.serviceWorker) { 
    navigator.serviceWorker .register('sw.js').then( registration => {
        console.log('service worker 注册成功')
    }).catch((err)=>{
        console.log('servcie worker 注册失败')
    })
} 
// sw.js  监听 `install` 事件，回调中缓存所需文件 
self.addEventListener('install', e => {
    // 打开指定的缓存文件名
    e.waitUntil(caches.open('my-cache').then( cache => {
        // 添加需要缓存的文件
        return cache.addAll(['./index.html', './index.css'])
    }))
})
// 拦截所有请求事件 缓存中有请求的数据就直接用缓存，否则去请求数据 
self.addEventListener('fetch', e => { 
    // 查找request中被缓存命中的response
    e.respondWith(caches.match(e.request).then( response => {
        if (response) {
            return response
        }
        console.log('fetch source')
    }))
})
```

---

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [为什么第二次打开页面快？五步吃透前端缓存，让页面飞起](https://juejin.cn/post/6993358764481085453)

