---
title: Axios源码解析
date: 2020-07-27 17:21:00
tags: Axios
---

## axios如何实现多种请求方式
---
#### `原理`： 
通过数组循环来批量注册接口，统一调用同一个方法，参数差异：通过until.merge()方法来解决差异。类型：通过方法循环的method来区分。
#### `实现逻辑`：

* 1. 生成axios实例；
* 2. 通过bind方法将axios原型中的request方法指向axios实例；
* 3. 通过extend方法将axios实例和axios的原型链合并并指向context
目的： 扩展axios的调用方法。使axios可以通过axios.get去调用，也可以通过原型链去调用。
* 4. 利用通过数组循环来批量注册接口，统一调用同一个方法.
通过until.merge()方法来解决参数差异。

`代码示例`：
```
function Axios (instanceConfig) {
  this.default = instanceConfig;
}
/** 
 * bind({}, {})
 * bind:将第一个参数的指向第二个参数;
 * extend({}, {})
 * extend:将两个对象进行合并;
*/
function creatInstance (defaultConfig) {
  let context = new Axios(defaultConfig);
  // 将Axios中的request方法，指向新的Axios对象
  let instance = bind(Axios.prototype.request, context);
  // 将前两个对象合并，合并后的this指向context
  // 目的：扩展axios的调用方法。使axios可以通过axios.get去调用，也可以通过原型链去调用。
  extend(instance, Axios.prototype, context);
  return instance;
}
// axios.get()

let arr = ['get', 'post', 'push ' ,'delete'];

arr.forEach((method) => {
  // 循环在原型链上注册请求方法。
  Axios.prototype[method] = function(url,config){
    // config :post的data,
    // util.merge :axios中的一个方法，原理对象合并
    return this.request(util.merge(config || {}, {
        methods: method,
        url: url
      })
    );
  };
})
```


## axios如何实现请求拦截
---

#### `实现逻辑`：
* 1. 在axios中配置拦截器 `interceptors`，拦截器包含request属性，和response属性，两个属性对应拦截器管理方法interceptorsManner。
* 2. interceptorsManner方法中配置handler属性，handler属性用于存放use加入的方法。
* 3. 在interceptorsManner的原型上配置use方法，user方法将参数fulilled, rejected两个方法存入对象中并存入到handler中。
* 4. 在axios的原型的request方法中注册数组chain  =  ['dispatchRequest', undefined];
 其中：request 会通过dispatchRequest将请求发送出去；
4.1 循环interceptors.request.handler将用户存入的请求拦截的两个参数方法通过unshift分别存放到数组中。
4.2 循环interceptors.response.handler将用户存入的响应拦截的两个参数方法通过shift分别存放到数组中。
 4.3 注册promise = Promise.resolve()；
4.4 循环chain，通过promise执行chain数组中的方法。
`示例代码`：
```
function Axios (instanceConfig) {
  this.default = instanceConfig;
  this.interceptors={
    request: new interceptorsManner(),
    response: new interceptorsManner(),
  };
}

Axios.prototype.request = function(){
  // request 会通过dispatchRequest将请求发送出去
  let chain = ['dispatchRequest', undefined];
  let promise = Promise.resolve();
  this.interceptors.request.handler.forEach((interceptor) => {
    chain.unshift(interceptor.fulilled, interceptor.rejected)
  })
  this.interceptors.response.handler.forEach((interceptor) => {
    chain.unshift(interceptor.fulilled, interceptor.rejected)
  // chain = [console.log(1), console.log(2),dispatchRequest,undefined, console.log(3), console.log(4)]
  })
  while(chain.length) {
    promise = promise.then(chain.shift(), chain.shift())
    // 举例
    // 第一次循环执行请求拦截前的成功和失败方法
    // 第二次循环执行发送请求和undefined;
    // 第一次循环执行响应拦截前的成功和失败方法
  }
  // 依次执行，并保证上一个方法完成之后在继续执行下一个方法。
};
function interceptorsManner () {
  this.handler= []; //存放use加入的方法
}
interceptorsManner.prototype.use = function use(fulilled, rejected) {
  this.handler.push({
    fulilled:  fulilled,
    rejected: rejected
  });
};
//请求拦截器&响应拦截器的使用方法
Axios.interceptors.request.use(function(){

},function(){

});
Axios.interceptors.response.use(function(){

},function(){

});
```
大功告成✌️✌️✌️

接下来你可能想要了解的：
[axios文档地址](https://www.npmjs.com/package/axios)
[Axios封装示例代码+封装思路](https://www.jianshu.com/p/7f7b655bd3c4)