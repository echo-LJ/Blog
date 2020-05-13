---
title: 探究vue-router的源码
date: 2020-05-13 16:30:54
tags: vue, vueRouter
---
Author: Echo
Time: 2020-05-13 16:30:54

## 一个vue路由的工作流程

---
#### 前端路由和后端路由的区别
* 后端路由
输入url  -->  请求发送到服务器 --> 服务器解析请求的路径 --> 浏览器拿取对应页面 --> 页面渲染
* 前端路由
输入url  -->  js解析地址 --> 找到对应地址的页面 --> 执行页面生成的js --> 页面渲染

#### vue-router的工作流程
![image.png](https://upload-images.jianshu.io/upload_images/11846892-ae149ebe3bad8a05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Hash与History
###### hash和history的使用
`hash: `
*  `#`号后面的就是hash的内容
*  通过location.hash来获取
* 通过onhashchange监听hash的改变

`history: `
*  history即正常的路径
*  通过location.pathname来获取
* 通过onpopstate监听history的改变

##实现vue-router源码实例
---
```
class HistoryRoute {
  constructor(){
    this.current = null;
  }
};
/**
 * options: newRouter时传入的参数；
*/
class VueRouter{
  constructor(options){
    this.mode = options.mode || 'hash';
    this.routes= options.routes || [];
    // 此处可以直接令this.history.current = null；但是为了记录前后路由跳转历史，生成HistoryRoute类
    this.history = new HistoryRoute;
    this.routesMap =  this.creatMap(this.routes);
    this.init(); //初始化路由
  },
  init () {
    // 触发监听事件
    // 改变vue-router中的current变量
    if (this.mode == 'hash') {
      // 根据hash的值自动在url上增加hash
      location.hash ? '': location.hash = '/';
      window.addEventListener('load',() => {
        this.history.current = location.hash.slice(1);
      })
      window.addEventListener('hashchange',() => {
        this.history.current = location.hash.slice(1);
      })
    } else {
      // 根据hash的值自动在url上增加hash
      location.pathname ? '': location.pathname = '/';
      window.addEventListener('load',() => {
        this.history.current = location.pathname;
      })
      window.addEventListener('hashchange',() => {
        this.history.current = location.pathname;
      })
    }
  }
  creatMap(routes){
    // 将routes转化成键值对形式 ‘/’: Hello
    // reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。
    // 参数一：初始值, 或者计算结束后的返回值。
    // 参数二：当前元素
    return routes.reduce((memo, current)=> {
      memo[current.path] = current.component;
      return memo
    })
  }
}
VueRouter.install = function (Vue) {
  // 监视current变量的监视者
  Vue.mixin({ //mixin会注入到每个组件
    // 查找到在根实例上放入的VueRouter对象，然后注入到每个组件。
    beforeCreate() {
      // this.$option && this.$options.router:当前这个组件的配置&& 当前组件上已经加载过route路由对象
      if (this.$options && this.$options.router) {
        // 将当前的实例，挂在到当前实例的_root变量上。
        this._root = this;
        // 将当前router 注入到当前实例（组件）的_router上。
        this._router = this.$options.router;
        // 在当前组件this下面中的this._router.history变量上对current进行监听
        Vue.util.defineReactive(this,'current', this._router.history);
      } else {
        // 逐级向上查找是否挂载
        this._root = this.$parent._root;
      }

      // 扩展知识 组件使用this.$router和this.$route
      // 在this上注册$router属性，并且不能更改。
      Object.defineProperty(this, '$router', {
        get () {
          return this._root._router;
        },
      })
      Object.defineProperty(this, '$route', {
        get () {
          return this._root._router.history.current;
        },
      })
    }
  });
  Vue.component('route-view', {
    render(h){
      // 根据当前current获取到对应的路径;
      // _self是vue自带的，_self指向实例自身；
      let current = this._self._root._router.history.current;
      let routeMap = this._self._root._router.routesMap;
      return h(routeMap[current]) //h：渲染作用
    }
  })
}
// 暴露vue类
export default VueRouter;
```

直接在项目中引入就可以啦！！
