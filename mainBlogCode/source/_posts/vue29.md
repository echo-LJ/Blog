---
title: Vue项目权限管理如何做？控制到按钮级别怎么做？
date: 2023-07-10 13:35:03
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>



综合实践题目: 页面权限和按钮权限,从这两个方便论述既可。

## 思路分析
1、权限管理需求分析:页面和按钮权限。
2、权限管理的实现方案: 分前端方案和后端方案阐述。
3、说说各自的优缺点。

## 回答范例

1、权限管理需求一般分为`页面权限`和`按钮权限`的管理。
2、具体实现方案分`前端实现`和`后端实现`两种:
    项目工程较小,需求变动小,维护工作量少,主要以前端实现为主。
    * **前端方案:**会把所有路由信息在前端配置,通过路由守位要求用户登录,用户登录后根据用户角色过滤出路由表。
    举例:配置一个`asyncRoutes`数组,需要认证的页面在其路由的`meta`中添加一个`roles`字段,等获取用户角色之后取两者交集,若结果不为空则说明可以访问。此过滤结束,剩下的路由就是该角色用户可以访问的页面,通过`router.addRoutes(accessRoutes)`方式添加动态路由即可。
    * **后端方案:** 会把所有的路由页面存储在数据库中,用户登录的时候根据其角色查到可以访问的路由页面信息返回给前端,前端通过`addRoute`动态添加路由信息。
    * **按钮权限:**的控制通常会通过一个指令`v-permission`,将按钮要求角色通过值传给指令,在指令的`mounted`钩子中可以判断当前用户角色和按钮是否存在交集,有则保留,无则移除。
3、纯前端方案实现简单,不需要额外权限管理页面,通过循环过滤即可,但是维护起来问题比较大,有新的页面和角色需求就需要重新修改前端代码打包部署,服务端不存在这个问题,通过专门的角色和权限管理页面,配置页面和按钮权限信息到数据库,应用每次获取到的都是最新的路由信息,可谓一劳永逸。


vue-element-admin:前端路由守位时： 登录-获取用户信息-过滤路由。
后端返回路由的名称，对返回的路由生成动态的路由map，生成对应的组件配置对象，存在一定复杂性。

## 可能追问
1、类似`Tabs`这类组件能不能使用`v-permission`指令实现按钮权限控制
```
<el-tabs>
    <el-tab-pane label="用户管理" name="first"></el-tab-pane>
    <el-tab-pane label="用户管理" name="third"></el-tab-pane>
</el-tab>
```
**不能**：v-permission原理时dom级别的删除，不是组件级别的删除，el-tab-pane可能产生一个页签等，所以可以借助`v-if`来实现。
2、服务端返回的路由信息如何添加到路由器中？

服务端一般存储的是组件名称，前端不能识别，前端通过map映射获取路由的动态配置信息。

---
总结:大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

