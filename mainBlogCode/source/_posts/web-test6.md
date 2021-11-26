---
title: 其他
date: 2021-01-26 17:59:37
tags:
---
<meta name="referrer" content="no-referrer"/>

## 1.Token一般存放在哪里？Token放在cookie和放在webStorage中有何不同？
**Token一般存放在哪里**
Token是访问资源的凭证：一般是用户通过用户名和密码登录之后，服务器将登录凭证做数字签名，加密之后得到的字符串为token.
它在用户登录成功之后返回给客户端，客户端存储主要通过一下几种方式：
* 存储在lockStorage中，每次调用接口的时候，把它当作字符串传给后端。
* 存储在cookie中，自动发送，缺点是不能跨域
* 存储在lockStorage中，每次调用接口的时候放在http请求头的Authorization中。

**Token放在cookie和放在webStorage中有何不同**
* Token存放在webStorage中
web存储可以通过同一域上的javascript访问，网站上的javascript都可以访问web存储，容易受到XSS（跨站脚本攻击）攻击
* Token存放在cookie中
优点：可以指定httponly，防止javascript读取，也可以指定secure,保证token只在https中传输
缺点：不符合Restful最佳实践，容易受到CSRF（跨站请求伪造）攻击


