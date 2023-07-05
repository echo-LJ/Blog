---
title: Vue 的双向绑定原理
date: 2023-04-20 11:12:22
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>


## 思路分析
1、给出双向绑定的定义
2、双向绑定的好处
3、在哪里使用双向绑定
4、使用方式、使用细节、vue3的变化
5、原理实现描述

## 回答范例
1、vue中双向绑定是一个指令：`v-model`, 可以绑定一个响应式数据到视图， 同时视图中变化能改变该数据值。
2、`v-model`是语法糖，默认情况下相当于：value 和 @input。(根据当前绑定的元素的类型不同，事件行为不同，自定义事件则更不相同)。使用`v-model`可以减少大量繁琐的事件处理代码，以及属性的绑定，提高开发效率，提升开发体验。
3、通常在表单上使用`v-model`,还可以在自定义组件中使用，表示某个值的输入和输出控制。
4、通过`<input v-model="xxx">`的方式将`变量值`绑定到表单元素value上，
    input类型不同，绑定方式有所不同：
* 对于checkbox,可以使用`ture-value` 或`false-value`,指定特殊的值。
* 对于radio可以使用value来指定特殊的值。
* 对于select 可以通过options元素的value来设置特殊的值。
* 还可以结合`.lazy`、`.number`、`.trim`来对v-model的行为作进一步限定。
* `v-model`用在自定义组件上会有很大的不同，

5、vue3中它类似于 vue2中的`sync`修饰符，最终展开的结果是`modelValue`属性的绑定和`update:modelValue`事件；（直接影响自定义使用，需要声明一个`modelValue`属性和派发类似`update:modelValue`的自定义事件）
vue3中可以用参数形式指定多个不同的绑定，例如v-model:foo和v-modv-model：bar等，非常强大！

6、 `v-model`是一个指令，他的神奇魔法是vue编译器完成的，看过源码，v-model会输出一个渲染函数，渲染函数实际上还是vulue属性的绑定以及input事件监听（类型不同，属性和事件有所不同，参考第4点。）

## 扩展问题1: v-model和sync修饰符有什么区别
在vue3中v-model类似于vue2中的修饰符，展开的就是model后面的参数，如果不设置就是`modelValue`和`update:modelValue`，如果设置就是自定义的值。 vue2中v-model的控制权在于自定义组件的自己的model选项，vue3中的控制权在于父组件，父组件中设置什么属性，子组件中就使用什么属性和派发什么事件。

```
<input type="text" v-model="foo">

// 编译出的渲染函数如下：

_c('input', {
    directives: [{name: 'model', rawName: "v-model",value:(foo), expression: foo}],
    attrs: {type: "text"},
    domProps: {value: {foo}},
    on: {
        "input":function($event){
            if($event.target.composing) return ;
            foo = $event.target.value
        }
    }
})
```
## 扩展问题2: 自定义组件使用v-model如果想要改变事件名和属性名应该怎么做

## 原理阐述
vue数据双向绑定是通过`数据劫持结合发布者-订阅者模式`的方式来实现的

1、首先要对数据进行劫持监听，所以我们需要设置一个监听器Observer，用来监听所有属性。
2、如果属性发上变化了，就需要告诉订阅者Watcher看是否需要更新。
3、因为订阅者是有很多个，所以我们需要有一个消息订阅器Dep来专门收集这些订阅者，然后在监听器Observer和订阅者Watcher之间进行统一管理的。
4、我们还需要有一个指令解析器Compile，对每个节点元素进行扫描和解析，将相关指令（如v-model，v-on）对应初始化成一个订阅者Watcher，并替换模板数据或者绑定相应的函数，5、此时当订阅者Watcher接收到相应属性的变化，就会执行对应的更新函数，从而更新视图。
![image.png](https://uploadfiles.nowcoder.com/images/20210401/447785786_1617244068309/9D8309092D17FDF894C258A0C7241C26)
### 原理概述

Vue 的双向绑定仅仅是一种`语法糖`，其实现依赖于 JavaScript 的底层机制。双向绑定的原理本质上是`底层数据对象`和`视图`之间的数据同步.

Vue 的双向数据绑定机制是通过响应式化、Watch、Dep、以及自定义事件系统等多个关键机制实现的

* `响应式化-数据劫持:` Vue会在组件初始化之前,对数据对象进行遍历劫持，使用`Object.defineProperty`（ES5）或 `Proxy`（ES6）将每个属性转化为`getter/setter`，并收集依赖，从而实现对数据对象的监听，这个过程叫做“响应式化”.
* 在模版中渲染组件时，Vue 会创建一个 Watcher 对象，并且对模版中使用`v-model`指令的DOM元素进行特殊处理，`添加事件监听器`，绑定到组件实例上一个叫做"[指令对象](https://012-cn.vuejs.org/guide/custom-directive.html)"的数据结构上，将数据和视图建立对应的依赖关系。
* 在数据对象被修改时，Vue会触发相应事件，通知 Watcher 对象进行进一步更新，使其`绑定的组件`重新渲染视图，在更新视图时，Vue是重新渲染虚拟DOM,对比新旧的虚拟DOM,发现有变化的地方，对实际的DOM进行更新。`这种方式可以有效地减少不必要的 DOM 操作和渲染，提高应用的性能`。
* 在用户输入时，Vue利用`JavaScript`原生事件监听捕获用户的输入，实时地将该值更新到组件的数据对象的值上，从而实现数据的双向绑定。

**优点：** v-model是语法糖，默认相当于:value和@input，使用v-model可以减少大量繁琐的事件处理代码，提高开发效率。

**原生事件监听捕获用户的输入原理：** 
编译器根据表单元素的不同会展开不同的DOM属性和事件
* text类型的input和textarea会展开为value和input事件
* checkbox和radio类型的input会展开为checked和change事件
* select用value作为属性，用change作为事件

**v-model使用场景：** 
通常在表单项上使用，也可以在自定义组件上使用，表示对某个值的输入和输出的控制。 可以结合`lazy`、`number`、`trim`对v-model的行为做进一步限定
* lazy：在输入框中，v-model默认是在input事件中同步输入框的数据(除了提示中介绍的中文输入 法情况外)，使用修饰符.lazy会转变为在change事件中同步
* number：将value转换为number类型
* trim：去除value左右两侧的空格
**[指令对象](https://012-cn.vuejs.org/guide/custom-directive.html)**
* el: 指令绑定的元素
* vm: 拥有该指令的上下文 ViewModel
* expression: 指令的表达式，不包括参数和过滤器
* arg: 指令的参数
* raw: 未被解析的原始表达式
* name: 不带前缀的指令名
## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：

* [https://zhuanlan.zhihu.com/p/138710460](https://zhuanlan.zhihu.com/p/138710460)

