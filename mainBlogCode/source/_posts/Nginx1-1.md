---
title: 深入理解Nginx：模块开发与架构解析（第2版）-Web服务器
date: 2023-01-04 14:17:57
tags: Nginx
---
<meta name="referrer" content="no-referrer"/>

## Web服务器

* `Tomcat服务器`：面向Java语言、轻量级应用服务器。
`缺点`：可以说Tomcat 只能用做java服务器.
`优点`：动态解析容器，处理动态请求，是编译JSP/Servlet的容器。
`Tomcat有三个端口`： 1）8005：关闭tomcat通信接口  2）8009：与其他http服务器通信接口，用于http服务器集合 3）8080：建立http连接用，如浏览器访问.

* `Jetty服务器`：面向Java语言，Jetty的架构是基于Handler来实现的，主要的扩展功能都可以用Handler来实现，扩展简单。 Tomcat的架构是基于容器设计的，进行扩展是需要了解Tomcat的整体设计结构，不易扩展。 Jetty和Tomcat性能方面差异不大。 Jetty可以同时处理大量连接而且可以长时间保持连接，适合于web聊天应用等等。

* `IIS服务器`：IIS只能在Windows操作系统上运行。Windows作为服务器在稳定性与其他一些性能上都
不如类UNIX操作系统，因此，在需要高性能Web服务器的场合下，IIS可能会被“冷落”。

* `Apache服务器`：支持模块多，性能稳定，Apache本身是静态解析，适合静态HTML、图片等，但可以通过扩展脚本、模块等支持动态页面等。
`缺点`：配置相对复杂，自身不支持动态页面。
`优点`：相对于Tomcat服务器来说处理静态文件是它的优势，速度快。Apache是静态解析，适合静态HTML、图片等。Apche可以支持PHPcgiperl,但是要使用Java的话，需要Tomcat在Apache后台支撑，将Java请求由Apache转发给Tomcat处理。

* `Nginx服务器`：十分轻量级的HTTP服务器、是高性能的HTTP和反向代理服务器。特点是占有内存少，并发能力强，易于开发，部署方便。Nginx 支持多语言通用服务器。Nginx有动态分离机制，静态请求直接就可以通过Nginx处理，动态请求才转发请求到后台交由Tomcat进行处理。
`缺点`：Nginx 只适合静态和反向代理。
`优点`：负载均衡、反向代理、处理静态文件优势。Nginx 处理静态请求的速度高于Apache。

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [Nginx和Apache和Tomcat的区别及优缺点](https://juejin.cn/post/6971001286502285326)
