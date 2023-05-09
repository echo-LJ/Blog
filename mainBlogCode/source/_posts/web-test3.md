---
title: web-test3
date: 2021-01-25 17:47:58
tags:
---



1.写 React / Vue 项目时为什么要在列表组件中写key，其作用是什么?
vue和react都是采用diff算法来对比新旧虚拟节点，从而更新节点。在vue的diff函数中（建议先了解一下diff算法过程）。
在交叉对比中，当新节点跟旧节点头尾交叉对比没有结果时，会根据新节点的key去对比旧节点数组中的key，从而找到相应旧节点（这里对应的是一个key => index 的map映射）。如果没找到就认为是一个新增节点。而如果没有key，那么就会采用遍历查找的方式去找到对应的旧节点。一种一个map映射，另一种是遍历查找。相比而言。map映射的速度更快。
2.什么是防抖和节流？有什么区别？如何实现
防抖:触发高频事件后n秒内函数只会执行一次，如果n秒内高频事件再次被触发，则重新计算时间
思路：每次触发事件时都取消之前的延时调用方法
```
function debounce(fn) {
 let timeout = null; // 创建一个标记用来存放定时器的返回值
      return function () {
        clearTimeout(timeout); // 每当用户输入的时候把前一个 setTimeout clear 掉
        timeout = setTimeout(() => { // 然后又创建一个新的 setTimeout, 这样就能保证输入字符后的 interval 间隔内如果还有字符输入的话，就不会执行 fn 函数
          fn.apply(this, arguments);
        }, 500);
      };
    }
    function sayHi() {
      console.log('防抖成功');
    }

    var inp = document.getElementById('inp');
    inp.addEventListener('input', debounce(sayHi)); // 防抖
```
节流:高频事件触发，但在n秒内只会执行一次，所以节流会稀释函数的执行频率
思路：每次触发事件时都判断当前是否有等待执行的延时函数
```
function throttle(fn) {
      let canRun = true; // 通过闭包保存一个标记
      return function () {
        if (!canRun) return; // 在函数开头判断标记是否为true，不为true则return
        canRun = false; // 立即设置为false
        setTimeout(() => { // 将外部传入的函数的执行放在setTimeout中
          fn.apply(this, arguments);
          // 最后在setTimeout执行完毕后再把标记设置为true(关键)表示可以执行下一次循环了。当定时器没有执行的时候标记永远是false，在开头被return掉
          canRun = true;
        }, 500);
      };
    }
    function sayHi(e) {
      console.log(e.target.innerWidth, e.target.innerHeight);
    }
    window.addEventListener('resize', throttle(sayHi));
```
3.介绍下 Set、Map、WeakSet 和 WeakMap 的区别？
Set
1.成员不能重复
2.只有健值，没有健名，有点类似数组。
3.可以遍历，方法有add, delete,has
weakSet
成员都是对象
成员都是弱引用，随时可以消失。 可以用来保存DOM节点，不容易造成内存泄漏
不能遍历，方法有add, delete,has
Map
本质上是健值对的集合，类似集合
可以遍历，方法很多，可以干跟各种数据格式转换
weakMap
1.直接受对象作为健名（null除外），不接受其他类型的值作为健名
健名所指向的对象，不计入垃圾回收机制
不能遍历，方法同get,set,has,delete
4. 介绍下深度优先遍历和广度优先遍历，如何实现？
深度优先遍历DFS：
假设初始状态是图中所有顶点均未被访问，则从某个顶点v出发，首先访问该顶点然后依次从它的各个未被访问的邻接点出发深度优先搜索遍历图，直至图中所有和v有路径相通的顶点都被访问到。若此时尚有其他顶点未被访问到，则另选一个未被访问的顶点作起始点，重复上述过程，直至图中所有顶点都被访问到为止。
```
let deepTraversal1 = (node, nodeList = []) => {
  if (node !== null) {
    nodeList.push(node)
    let children = node.children
    for (let i = 0; i < children.length; i++) {
      deepTraversal1(children[i], nodeList)
    }
  }
  return nodeList
}
let deepTraversal2 = (node) => {
    let nodes = []
    if (node !== null) {
      nodes.push(node)
      let children = node.children
      for (let i = 0; i < children.length; i++) {
        nodes = nodes.concat(deepTraversal2(children[i]))
      }
    }
    return nodes
  }
```
广度优先遍历 BFS
从图中某顶点v出发，在访问了v之后依次访问v的各个未曾访问过的邻接点，然后分别从这些邻接点出发依次访问它们的邻接点，并使得“先被访问的顶点的邻接点先于后被访问的顶点的邻接点被访问，直至图中所有已被访问的顶点的邻接点都被访问到。 如果此时图中尚有顶点未被访问，则需要另选一个未曾被访问过的顶点作为新的起始点，重复上述过程，直至图中所有顶点都被访问到为止。
```
let widthTraversal2 = (node) => {
  let nodes = []
  let stack = []
  if (node) {
    stack.push(node)
    while (stack.length) {
      let item = stack.shift()
      let children = item.children
      nodes.push(item)
        // 队列，先进先出
        // nodes = [] stack = [parent]
        // nodes = [parent] stack = [child1,child2,child3]
        // nodes = [parent, child1] stack = [child2,child3,child1-1,child1-2]
        // nodes = [parent,child1,child2]
      for (let i = 0; i < children.length; i++) {
        stack.push(children[i])
      }
    }
  }
  return nodes
}
```
5.ES5/ES6 的继承除了写法以外还有什么区别？
class 声明会提升，但不会初始化赋值。Foo 进入暂时性死区，类似于 let、const 声明变量。
const bar = new Bar(); // it's ok
function Bar() {
  this.bar = 42;
}

const foo = new Foo(); // ReferenceError: Foo is not defined
class Foo {
  constructor() {
    this.foo = 42;
  }
}
class 声明内部会启用严格模式。
// 引用一个未声明的变量
function Bar() {
  baz = 42; // it's ok
}
const bar = new Bar();

class Foo {
  constructor() {
    fol = 42; // ReferenceError: fol is not defined
  }
}
const foo = new Foo();
class 的所有方法（包括静态方法和实例方法）都是不可枚举的。
// 引用一个未声明的变量
function Bar() {
  this.bar = 42;
}
Bar.answer = function() {
  return 42;
};
Bar.prototype.print = function() {
  console.log(this.bar);
};
const barKeys = Object.keys(Bar); // ['answer']
const barProtoKeys = Object.keys(Bar.prototype); // ['print']

class Foo {
  constructor() {
    this.foo = 42;
  }
  static answer() {
    return 42;
  }
  print() {
    console.log(this.foo);
  }
}
const fooKeys = Object.keys(Foo); // []
const fooProtoKeys = Object.keys(Foo.prototype); // []
class 的所有方法（包括静态方法和实例方法）都没有原型对象 prototype，所以也没有[[construct]]，不能使用 new 来调用。
function Bar() {
  this.bar = 42;
}
Bar.prototype.print = function() {
  console.log(this.bar);
};

const bar = new Bar();
const barPrint = new bar.print(); // it's ok

class Foo {
  constructor() {
    this.foo = 42;
  }
  print() {
    console.log(this.foo);
  }
}
const foo = new Foo();
const fooPrint = new foo.print(); // TypeError: foo.print is not a constructor
必须使用 new 调用 class。
function Bar() {
  this.bar = 42;
}
const bar = Bar(); // it's ok

class Foo {
  constructor() {
    this.foo = 42;
  }
}
const foo = Foo(); // TypeError: Class constructor Foo cannot be invoked without 'new'
class 内部无法重写类名。
function Bar() {
  Bar = 'Baz'; // it's ok
  this.bar = 42;
}
const bar = new Bar();
// Bar: 'Baz'
// bar: Bar {bar: 42}  

class Foo {
  constructor() {
    this.foo = 42;
    Foo = 'Fol'; // TypeError: Assignment to constant variable
  }
}
const foo = new Foo();
Foo = 'Fol'; // it's ok
6.编写一个程序将数组扁平化去并除其中重复部分数据，最终得到一个升序且不重复的数组
Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{ return a-b})
7.JS 异步解决方案的发展历程以及优缺点
1. 回调函数（callback）
setTimeout(() => {
    // callback 函数体
}, 1000)
缺点：回调地狱，不能用 try catch 捕获错误，不能 return

回调地狱的根本问题在于：

缺乏顺序性： 回调地狱导致的调试困难，和大脑的思维方式不符
嵌套函数存在耦合性，一旦有所改动，就会牵一发而动全身，即（控制反转）
嵌套函数过多的多话，很难处理错误
ajax('XXX1', () => {
    // callback 函数体
    ajax('XXX2', () => {
        // callback 函数体
        ajax('XXX3', () => {
            // callback 函数体
        })
    })
})
优点：解决了同步的问题（只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。）

2. Promise
Promise就是为了解决callback的问题而产生的。

Promise 实现了链式调用，也就是说每次 then 后返回的都是一个全新 Promise，如果我们在 then 中 return ，return 的结果会被 Promise.resolve() 包装

优点：解决了回调地狱的问题

ajax('XXX1')
  .then(res => {
      // 操作逻辑
      return ajax('XXX2')
  }).then(res => {
      // 操作逻辑
      return ajax('XXX3')
  }).then(res => {
      // 操作逻辑
  })
缺点：无法取消 Promise ，错误需要通过回调函数来捕获

3. Generator
特点：可以控制函数的执行，可以配合 co 函数库使用

function *fetch() {
    yield ajax('XXX1', () => {})
    yield ajax('XXX2', () => {})
    yield ajax('XXX3', () => {})
}
let it = fetch()
let result1 = it.next()
let result2 = it.next()
let result3 = it.next()
4. Async/await
async、await 是异步的终极解决方案

优点是：代码清晰，不用像 Promise 写一大堆 then 链，处理了回调地狱的问题

缺点：await 将异步代码改造成同步代码，如果多个异步操作没有依赖性而使用 await 会导致性能上的降低。

async function test() {
  // 以下代码没有依赖性的话，完全可以使用 Promise.all 的方式
  // 如果有依赖性的话，其实就是解决回调地狱的例子了
  await fetch('XXX1')
  await fetch('XXX2')
  await fetch('XXX3')
}
下面来看一个使用 await 的例子：

let a = 0
let b = async () => {
  a = a + await 10
  console.log('2', a) // -> '2' 10
}
b()
a++
console.log('1', a) // -> '1' 1
对于以上代码你可能会有疑惑，让我来解释下原因

首先函数 b 先执行，在执行到 await 10 之前变量 a 还是 0，因为 await 内部实现了 generator ，generator 会保留堆栈中东西，所以这时候 a = 0 被保存了下来
因为 await 是异步操作，后来的表达式不返回 Promise 的话，就会包装成 Promise.reslove(返回值)，然后会去执行函数外的同步代码
同步代码执行完毕后开始执行异步代码，将保存下来的值拿出来使用，这时候 a = 0 + 10
上述解释中提到了 await 内部实现了 generator，其实 await 就是 generator 加上 Promise的语法糖，且内部实现了自动执行 generator。如果你熟悉 co 的话，其实自己就可以实现这样的语法糖。
8.如何实现一个 new 
function _new(fn, ...arg) {
    const obj = Object.create(fn.prototype);
    const ret = fn.apply(obj, arg);
    return ret instanceof Object ? ret : obj;
}


### 10.介绍下观察者模式和订阅-发布模式的区别，各自适用于什么场景
观察者模式中主体和观察者是互相感知的，发布-订阅模式是借助第三方来实现调度的，发布者和订阅者之间互不感知
### 11.聊聊 Vue 的双向数据绑定，Model 如何改变 View，View 又是如何改变 Model 的
明明是单向绑定，只是 m -> v，在 vue 2.x 中 通过 defineProperty 实现的数据劫持，getter 收集依赖，setter 调用更新回调，这个过程是 vue 黑盒提供的，也就是说数据驱动视图，开发人员只需关注数据的变更即可；再说 v -> m，通过 v-model 的方式，指令添加是开发人员加的吧，如果一个组件有多个 v-model ，你要自己写 v-on 和 data 的修改吧¬