---
title: Vue 的响应式原理 和 双向绑定原理的区别
date: 2023-04-21 11:10:32
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

### 区别
`应用场景`和`实现机制`不同：


**监听方式不同**

`双向绑定`主要是通过 v-model 指令实现的，它通过在视图和 data 数据中同步绑定一个属性，当视图中输入值时就会直接更新 data 对应的值。比如在 input 标签上通过 v-model 实现双向绑定就是通过监听 input 事件和 data 的变化更新两点的。

`响应式更新`则主要是通过 Vue 的依赖收集进行实时监听来实现，Vue 会在模板渲染过程中触发 getter 函数，把视图最初渲染时需要用到的响应式数据对应的 Watcher 对象存入该对象内部的一个数组中，当数据变化时，就会触发 setter 函数，然后通知它的 Watcher 们，让它们重新执行以来 Vue 模板中这些数据的 getter 函数，从而更新视图。

**实现原理不同**

`双向绑定`实现方式通常是通过在模板中使用类似v-model的指令，将视图和数据进行绑定，然后根据具体实现方式，使用某种机制（如 DOM 事件、数据监听等）来使双向绑定生效。在 Vue 中，双向绑定是通过实现双向绑定指令和数据监听来实现的。

`响应式更新`通过在数据对象中使用某种机制（如 ES6 中的 Proxy 对象、Object.defineProperty 等）来劫持数据属性的读取和设置操作，以便在需要更新视图时自动触发对应的回调函数。在 Vue 中，响应式是通过使用 ES6 中的 Proxy 对象来实现的。

**作用不同**

`双向绑定`是让视图和数据实现双向绑定，数据改变时视图随之更新，视图改变时数据也会随之改变。

`响应式更新`是为了实现数据自动监测，当数据发生变化时，自动更新视图，提高开发效率。

### Vue 的 observe 方法 和Object.defineProperty 的区别
**监听范围**
1、`Object.defineProperty` 只能监听对象中的`属性`变化，并不能动态添加属性的监听.
2、`Vue 的 observe` 方法可以直接监听`整个对象`或数组及其`属性`的变化，。



**实现原理**

1、`Object.defineProperty`采用Hack方式，在原型上添加`set`、`remove`等方法,进行数组元素的添加`Vue.$set`/删除监听.通过对数组的 `push`、`pop`、`shift`、`unshift`、`splice`、`sort`、`reverse` 等方法进行劫持，在这些方法被触发时，才能通过`setter`方法做相应的操作。
`Object.defineProperty` 并不能直接监听对象本身或数组的变化，即对象或数组的引用变化，不会触发 setter 方法
2、`Vue 的 observe`无需使用Hack的方式进行数组元素的监听，采用递归遍历对象属性的方式，为其所有属性添加`getter`和`setter`来实现数据的监听，从而可以监听到对象或数组的任何变化，包括引用变化以及动态添加删除属性/元素等操作


**性能**

1、`Object.defineProperty` 对象属性的监听是一次性完成的,
2、`Vue 的 observe`遍历监听整个对象属性时会影响性能，特别是对于大型对象.
3、Vue.js 3.0中引入了 `Proxy` 来替代 Object.defineProperty，按需监听数据属性，从而提高了性能

### Object.defineProperty 和 proxy的区别
Object.defineProperty 和 Proxy 是 JavaScript 的两种对象劫持:

**兼容性**
1、`Object.defineProperty`可以兼容 ES5 及之前的版本。
2、`Proxy` 只能在 ES6（ECMAScript 2015）及之后的版本中使用。
3、如果要在无法使用 Proxy这些环境中使用 Proxy，可以考虑使用 polyfill 或类似的工具来实现 Proxy 的功能。

**劫持对象**

1、`Object.defineProperty`可以劫持对象的 get 和 set 方法,可以劫持对象的单个属性，无法单独劫持一个对象本身。
返回方式：
2、 `Proxy` 可以劫持对象的 get、set、has 等多种方法，也可以对整个对象做出拦截和处理。。

**返回方式**
1、`Object.defineProperty` 返回值是被劫持的对象本身,为了支持链式调用的编程风格，从而让属性设置更加简便和易用.
```
let obj = {};
Object.defineProperty(obj, 'name', {
  value: 'Tom',
  writable: false,
});

Object.defineProperty(obj, 'age', {
  value: 18,
  writable: true,
});

console.log(obj);  // { name: 'Tom', age: 18 }

```
2、`Proxy`  返回值是代理对象（Proxy Object）,通过代理对象访问原对象的属性，并在访问时拦截和处理相关的操作。



```
let obj = { a: 1 };
console.log(obj.a) // 1
let proxy = new Proxy(obj, {
  get(target, property) {
    console.log(`Getting ${property} value: ${target[property]}`);
    return target[property];
  },
  set(target, property, value) {
    console.log(`Setting ${property} value: ${value}`);
    target[property] = value;
  }
});

proxy.a = 2;
console.log(proxy.a); // 2
console.log(obj.a); // 2
```







**性能**
`Proxy` 的性能可以比 `Object.defineProperty` 高出两倍或更多.





### Proxy 可以劫持的各种方法的作用：

**get(target, property, receiver)：** 拦截对象属性的读取，返回代理对象读取的值。
* `target`：表示被代理的对象.
* `property`：表示被访问的属性名称。
* `receiver`：表示最初被调用的对象，一般情况下是代理对象本身，但在继承时可能会有所不同。
```
const person = {
  name: 'Bob',
};

const proxy = new Proxy(person, {
  get(target, property, receiver) {
    console.log(`Getting ${property} value: ${target[property]}`);
    return target[property];
  },
});

console.log(proxy.name);
```
**set(target, property, value, receiver)：**拦截对象属性的写入，将代理对象写入的值赋予目标对象。
* value：表示被设置的属性值。
```
const person = {
  name: 'Bob',
};
console.log(person.name); //Bob
const proxy = new Proxy(person, {
  set(target, property, value, receiver) {
    console.log(`Setting ${property} value: ${value}`);
    target[property] = value;
    return true;
  },
});

proxy.name = 'Alice';
console.log(person.name); // Alice
```
**has(target, property)：**拦截 in 运算符，返回一个布尔值，表示目标对象是否具有某个属性。
**construct(target, args)：**拦截 new 运算符，返回一个对象。
**deleteProperty(target, property)：**拦截 delete 运算符，返回一个布尔值，表示目标对象中是否删除某个属性。
**getPrototypeOf(target)：**拦截获取对象原型链。
**setPrototypeOf(target, proto)：**拦截设置对象原型链。
**isExtensible(target)：**拦截判断对象身是否可扩展。
**preventExtensions(target)：**拦截对象身不再可扩展。
**getOwnPropertyDescriptor(target, property)：**拦截获取对象属性的描述符。
**defineProperty(target, property, descriptor)：**拦截对象属性的定义。


### Object.defineProperty的缺点
* 不能监听数组；因为数组没有getter和setter，因为数组长度不确定，如果太长性能负担太大

* 只能监听属性，而不是整个对象；需要遍历属性；

* 只能监听属性变化，不能监听属性的删减；

### Proxy好处
* Proxy可以监听数组，不用单独处理数组；
* Object.defineProperty需要指定对象和属性，对于多层嵌套的对象需要递归监听,Proxy可以直接监听整个对象，不需要递归；
* Object.defineProperty的get方法没有传入参数，如果我们需要返回原值，需要在外部缓存一遍之前的值，Proxy的get方法可以传入对象和属性，可以直接在函数内部操作，不需要外部变量；
* set方法也有类似的问题，Object.defineProperty的set方法传入参数只有newValue，也需要手动将newValue赋给外部变量，Proxy的set也会传入对象和属性，可以直接在函数内部操作；
* new Proxy()会返回一个新对象，不会污染源原对象；

### Proxy缺点
兼容性不好，且无法用polyfill磨平；

## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：

* [Vue双向绑定原理](https://blog.nowcoder.net/n/8517450fe4fd4220b4078f9c61e42ec1)
