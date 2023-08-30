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