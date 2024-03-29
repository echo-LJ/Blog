---
title: 面试官：“你能手写一个 Promise 吗”
date: 2022-04-12 18:50:57
tags:
---
<meta name="referrer" content="no-referrer"/>

## 一、Promise 出现的原因 & 业界实现

在 Promise 出现以前，在我们处理多个异步请求嵌套时，代码往往是这样的...

```
let fs = require('fs')

fs.readFile('./name.txt','utf8',function(err,data){
  fs.readFile(data, 'utf8',function(err,data){
    fs.readFile(data,'utf8',function(err,data){
      console.log(data);
    })
  })
})。
```

为了拿到回调的结果，我们必须一层一层的嵌套，可以说是相当恶心了。而且基本上我们还要对每次请求的结果进行一系列的处理，使得代码变的更加难以阅读和难以维护，这就是传说中臭名昭著的`回调地狱`～产生回调地狱的原因归结起来有两点：

* 1、**嵌套调用**，第一个函数的输出往往是第二个函数的输入；
* 2、**处理多个异步请求并发**，开发时往往需要同步请求最终的结果。

原因分析出来后，那么问题的解决思路就很清晰了：

* 1、**消灭嵌套调用**，通过 Promise 的链式调用可以解决；
* 2、**合并多个任务的请求结果**，使用 Promise.all 获取合并多个任务的错误处理。

Promise 正是用一种更加友好的代码组织方式，解决了异步嵌套的问题。

```
let fs = require('fs')

function read(filename) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, 'utf8', (err, data) => {
      if (err) reject(err);
      resolve(data);
    })
  })
}

read('./name.txt').then((data)=>{
  return read(data) 
}).then((data)=>{
  return read(data)  
}).then((data)=>{
    console.log(data);
},err=>{
    console.log(err);
})
```
臃肿的嵌套变得线性多了有木有？没错，他就是我们的异步神器 Promise！


让我们再次回归刚才的问题，**Promise为我们解决了什么问题？** 在传统的异步编程中，如果异步之间存在依赖关系，就需要通过层层嵌套回调的方式满足这种依赖，如果嵌套层数过多，可读性和可以维护性都会变得很差，产生所谓的“回调地狱”，而 Promise 将嵌套调用改为链式调用，增加了可阅读性和可维护性。也就是说，Promise 解决的是异步编码风格的问题。 那 **Promise 的业界实现都有哪些呢？** 业界比较著名的实现 Promise 的类库有 bluebird、Q、ES6-Promise。


## 一、从零开始，手写 Promise

我们想要手写一个 Promise，就要遵循  [Promise/A+](https://promisesaplus.com/) 规范，业界所有 Promise 的类库都遵循这个规范。
其实 Promise/A+ 规范对如何实现一个符合标准的 Promise 类库已经阐述的很详细了。每一行代码在 Promise/A+ 规范中都有迹可循，所以在下面的实现的过程中，我会尽可能的将代码和 Promise/A+ 规范一一对应起来。
下面开始步入正题啦～

**基础版 Promise**

我们先来回顾下最简单的 Promise 使用方式：

```
const p1 = new Promise((resolve, reject) => {
  console.log('create a promise');
  resolve('成功了');
})

console.log("after new promise");

const p2 = p1.then(data => {
  console.log(data)
  throw new Error('失败了')
})

const p3 = p2.then(data => {
  console.log('success', data)
}, err => {
  console.log('faild', err)
})

```

控制台输出：


```
"create a promise"
"after new promise"
"成功了"
"faild Error: 失败了"
```

* 首先我们在调用 Promise 时，会返回一个 Promise 对象。
* 构建 Promise 对象时，需要传入一个 executor 函数，Promise 的主要业务流程都在 executor 函数中执行。
* 如果运行在 excutor 函数中的业务执行成功了，会调用 resolve 函数；如果执行失败了，则调用 reject 函数。
* Promise 的状态不可逆，同时调用 resolve 函数和 reject 函数，默认会采取第一次调用的结果。

以上简单介绍了 Promise 的一些主要的使用方法，结合 Promise/A+ 规范，我们可以分析出 Promise 的基本特征：

>
1、promise 有三个状态： pending, fulfilled、rejected;「规范 Promise/A+ 2.1」
2、new promises 时，需要传递一个 executor()执行器，执行期立即执行；
3、executor接收两个参数，分别是resolve和reject；
4、promise的默认状态是pending;
5、promise有一个value保存成功状态的值，可以是undefined/thenable/promise；「规范 Promise/A+ 1.3」
6、promise 有一个reason保存失败状态的值；「规范 Promise/A+ 1.5」
7、promise 只能从pending到rejected, 或者从pending到fulfilled，状态一旦确认，就不会再改变；
8、promise 必须有一个then方法，then 接收两个参数，分别是 promise 成功的回调 onFulfilled, 和 promise 失败的回调 onRejected；「规范 Promise/A+ 2.2」
9、如果调用 then 时，promise 已经成功，则执行onFulfilled，参数是promise的value；
10、如果调用 then 时，promise 已经失败，那么执行onRejected, 参数是promise的reason；
11、如果 then 中抛出了异常，那么就会把这个异常作为参数，传递给下一个 then 的失败的回调onRejected；


按照上面的特征，我们试着勾勒下 Promise 的形状：

```

// 三个状态：PENDING、FULFILLED、REJECTED
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED';
const REJECTED = 'REJECTED';

class Promise {
  constructor(executor) {
    // 默认状态为 PENDING
    this.status = PENDING;
    // 存放成功状态的值，默认为 undefined
    this.value = undefined;
    // 存放失败状态的值，默认为 undefined
    this.reason = undefined;

    // 调用此方法就是成功
    let resolve = (value) => {
      // 状态为 PENDING 时才可以更新状态，防止 executor 中调用了两次 resovle/reject 方法
      if(this.status ===  PENDING) {
        this.status = FULFILLED;
        this.value = value;
      }
    } 

    // 调用此方法就是失败
    let reject = (reason) => {
      // 状态为 PENDING 时才可以更新状态，防止 executor 中调用了两次 resovle/reject 方法
      if(this.status ===  PENDING) {
        this.status = REJECTED;
        this.reason = reason;
      }
    }

    try {
      // 立即执行，将 resolve 和 reject 函数传给使用者  
      executor(resolve,reject)
    } catch (error) {
      // 发生异常时执行失败逻辑
      reject(error)
    }
  }

  // 包含一个 then 方法，并接收两个参数 onFulfilled、onRejected
  then(onFulfilled, onRejected) {
    if (this.status === FULFILLED) {
      onFulfilled(this.value)
    }

    if (this.status === REJECTED) {
      onRejected(this.reason)
    }
  }
}
```

写完代码我们可以测试一下：

```
const promise = new Promise((resolve, reject) => {
  resolve('成功');
}).then(
  (data) => {
    console.log('success', data)
  },
  (err) => {
    console.log('faild', err)
  }
)
// (resolve, reject) => { resolve('成功'); } 这个等于executor(resolve,reject)， 会立即进入构造函数的try catch这一行
```

控制台输出：

```
"success 成功"

```

现在我们已经实现了一个基础版的 Promise，但是还不要高兴的太早噢，这里我们只处理了同步操作的 promise。如果在 executor()中传入一个异步操作的话呢，我们试一下：

```
const promise = new Promise((resolve, reject) => {
  // 传入一个异步操作
  setTimeout(() => {
    resolve('成功');
  },1000);
}).then(
  (data) => {
    console.log('success', data)
  },
  (err) => {
    console.log('faild', err)
  }
)


```

执行测试脚本后发现，promise 没有任何返回。

因为 promise 调用 then 方法时，当前的 promise 并没有成功，一直处于 pending 状态。所以如果当调用 then 方法时，当前状态是 pending，我们需要先将成功和失败的回调分别存放起来，在executor()的异步任务被执行时，触发 resolve 或 reject，依次调用成功或失败的回调。
结合这个思路，我们优化一下代码：


```
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED';
const REJECTED = 'REJECTED';

class Promise {
  constructor(executor) {
    this.status = PENDING;
    this.value = undefined;
    this.reason = undefined;
    // 存放成功的回调
    this.onResolvedCallbacks = [];
    // 存放失败的回调
    this.onRejectedCallbacks= [];

    let resolve = (value) => {
      if(this.status ===  PENDING) {
        this.status = FULFILLED;
        this.value = value;
        // 依次将对应的函数执行
        this.onResolvedCallbacks.forEach(fn=>fn());
      }
    } 

    let reject = (reason) => {
      if(this.status ===  PENDING) {
        this.status = REJECTED;
        this.reason = reason;
        // 依次将对应的函数执行
        this.onRejectedCallbacks.forEach(fn=>fn());
      }
    }

    try {
      executor(resolve,reject)
    } catch (error) {
      reject(error)
    }
  }

  then(onFulfilled, onRejected) {
    if (this.status === FULFILLED) {
      onFulfilled(this.value)
    }

    if (this.status === REJECTED) {
      onRejected(this.reason)
    }

    if (this.status === PENDING) {
      // 如果promise的状态是 pending，需要将 onFulfilled 和 onRejected 函数存放起来，等待状态确定后，再依次将对应的函数执行
      this.onResolvedCallbacks.push(() => {
        onFulfilled(this.value)
      });

      // 如果promise的状态是 pending，需要将 onFulfilled 和 onRejected 函数存放起来，等待状态确定后，再依次将对应的函数执行
      this.onRejectedCallbacks.push(()=> {
        onRejected(this.reason);
      })
    }
  }
}

```
测试一下：
```
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('成功');
  },1000);
}).then(
  (data) => {
    console.log('success', data)
  },
  (err) => {
    console.log('faild', err)
  }
)

```

控制台等待 1s 后输出：
```
"success 成功"
```


ok！大功告成，异步问题已经解决了！

熟悉设计模式的同学，应该意识到了这其实是一个发布订阅模式，这种收集依赖 -> 触发通知 -> 取出依赖执行的方式，被广泛运用于发布订阅模式的实现。

then 的链式调用&值穿透特性
我们都知道，promise 的优势在于可以链式调用。在我们使用 Promise 的时候，当 then 函数中 return 了一个值，不管是什么值，我们都能在下一个 then 中获取到，这就是所谓的then 的链式调用。而且，当我们不在 then 中放入参数，例：promise.then().then()，那么其后面的 then 依旧可以得到之前 then 返回的值，这就是所谓的值的穿透。那具体如何实现呢？简单思考一下，如果每次调用 then 的时候，我们都重新创建一个 promise 对象，并把上一个 then 的返回结果传给这个新的 promise 的 then 方法，不就可以一直 then 下去了么？那我们来试着实现一下。这也是手写 Promise 源码的重中之重，所以，打起精神来，重头戏来咯！


有了上面的想法，我们再结合 Promise/A+ 规范梳理一下思路：

>
1、then 的参数 onFulfilled 和 onRejected 可以缺省，如果 onFulfilled 或者 onRejected不是函数，将其忽略，且依旧可以在下面的 then 中获取到之前返回的值；「规范 Promise/A+ 2.2.1、2.2.1.1、2.2.1.2」
2、promise 可以 then 多次，每次执行完 promise.then 方法后返回的都是一个“新的promise"；「规范 Promise/A+ 2.2.7」
3、如果 then 的onResolved处理程序的返回值 x 是一个普通值，那么就会把这个结果作为参数，传递给下一个 then 的成功的回调中；
4、如果 then 中抛出了异常，那么就会把这个异常作为参数，传递给下一个 then 的失败的回调中；「规范 Promise/A+ 2.2.7.2」
5、如果 then 的的onResolved处理程序的返回值 x 是一个 promise，那么会等这个 promise 执行完，promise 如果成功，就走下一个 then 的成功；如果失败，就走下一个 then 的失败；如果抛出异常，就走下一个 then 的失败；「规范 Promise/A+ 2.2.7.3、2.2.7.4」
6、如果 then 的onResolved处理程序的返回值 x 和 promise 是同一个引用对象，造成循环引用，则抛出异常，把异常传递给下一个 then 的失败的回调中；「规范 Promise/A+ 2.3.1」
7、如果 then 的onResolved处理程序的返回值 x 是一个 promise，且 x 同时调用 resolve 函数和 reject 函数，则第一次调用优先，其他所有调用被忽略；「规范 Promise/A+ 2.3.3.3.3」
我们将代码补充完整：
```
const PENDING = 'PENDING';
const FULFILLED = 'FULFILLED';
const REJECTED = 'REJECTED';
```

参考链接:
* [面试官：“你能手写一个 Promise 吗”](https://juejin.cn/post/6850037281206566919)

