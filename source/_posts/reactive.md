---
title: Vue响应式原理解析
date: 2020-03-30 16:55:06
tags: vue
---
### vue从改变一个数据到发生改变的过程
* 数据改变触发set
* Set部分触发notify(更新和通知) -> Get部分收集依赖
* 更改对应的虚拟dom
* 重新render
#### vue2
* defineProperty
defineProperty不仅仅用来做响应式，他的作用可以用来给对象中的属性配置操作权限。其中get和set实现了响应式原理。

```
let ob = {
  a:1
}
Object.defineProperty(ob, 'a', {
  writable:false, // 属性值是否可写
  enumerable: false, // 属性值是否可循环
  configurable: false //属性值是否可以delete
})

Object.getOwnPropertyDescriptor(ob, 'a'); // 读取对象的属性值
```
* get & set 
```
let _value = ob.a;
// 如果想要使用get&set方法实现读取和存储，必须将属性值重新赋值给新的变量，来实现储存和中转，这也是defineProperty不优雅的一点。
// get & set 必须配合return方法使用。
Object.defineProperty(ob, 'a', {
  get () {
    return _value;
  },
  set (newValue) {
    _value = newValue
    return _value;
  }
})
```


```
// 实现vue简易版的双向原理
function Vue () {
  this.$data = {a:1};
  this.el = document.getElementById('app');
  this.vritualDom = '';
  this.observer(this.$data); //注册监听
  this.render();
}
// 注册get和set
Vue.prototype.observer = function (obj) {
  var value;
  var self = this;
  for (var key in obj) {
    value = obj[key];
    if (typeof value === 'object') {
      this.observer(value);
    } else {
      Object.defineProperty(obj, key, {
        get(){
          // 进行依赖收集
          // 为什么要收集依赖？
          // 数据可能只在局部位置使用，找到对应依赖，只更新依赖于变量的组件。
          return value;
        },
        set(newvalue){
          value = newvalue;
          self.render()
          return value;
        }
      })
    }
  }
};

Vue.prototype.render = function (obj) {
  this.vritualDom = 'i am' + this.$data.a;
  this.el.innerHTML = this.vritualDom;
}
```


### defineProperty中的get和set是对象的属性，对数组如何实现处理？

```
// 数组的双向绑定就是做了一个设计者模式；
// 取出数组的原型链并拷贝;
var arraypro = Array.prototype;
var arrob =Object.create(arraypro);
var arrFun = ['push', 'pop', 'shift'];

arr.forEach((method .index) => {
  arrob[method] = function () {
    var ret = arraypro[method].apply(this, arguments);
    dep.notify() // 触发试图更新
  }
})
```
### vue3
* Proxy对象用于定义基本操作的自定义行为（和defineProperty类似，用法不同）
```
let obj = {
  a:1,
}
var obj = new Proxy(ob, {
  get (target, key, receiver) {
    return target[key];
  },
  set (target, key, value, receiver) {
    return Reflect.set(target, key, value)
    // return target[key] = value;
  }
})
```



```
// vue简易版的实现双向原理
function Vue () {
  var self = this;
  this.$data = new Proxy(this.$data, {
    get (target, key, receiver) {
      return target[key];
    },
    set (target, key, value, receiver) {
      target[key] = value;
      self.render();
      return target[key];
    }
  })
}

```


#### proxy的其他作用？
* 校验类型
 // 创建一个对象，对象有name和age两个属性;name是中文 age是数字且大于18;
```
var validtor = {
  // 策略模式
  name: function (value) {
    var reg = /^[\u4E00-\u9FA5]+$/;
    if (typeof value == 'string' && reg.test(value)) {
      return true;
    }
    return false;
  },
  age: function (value) {
    if (typeof value == 'number' && value >= 18) {
      return true;
    }
    return false;
  },
}
function preson (age, name) {
  this.age = age;
  this.name = name;
  return new Proxy(this, {
    get (target, key) {
      return targe[key]
    },
    set (target, key, value) {
      if (validtor[key](value)) {
        return Reflect.set(target, key, value);
      } else {
        throw new Error(key + 'is not right')
      }
    }
  })
};
```
* 真正私有属性

#### defineProperty 和 Proxy的区别
1. defineProperty只能监听对象的某个属性，不能监听全对象；
2. 可以省去defineProperty 中的for in 循环，节省性能
3. 可以监听数组，不需要再去单独的对数组进行特异性处理；
4. 不需要借助外部变量实现存储和读取（完善了vue2中defineProperty的不优雅的地方）
3. 不会污染原对象(proxy对原对象进行代理，生成新的代理对象)

