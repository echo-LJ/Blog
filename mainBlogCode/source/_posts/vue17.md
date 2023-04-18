---
title: Vue组件通信方式
date: 2023-04-17 11:03:09
tags: Vue.js
---



<meta name="referrer" content="no-referrer"/>


### 组件通信常用方式有以下九种

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b81aa2acb07446daaf97e001310c9ce~tplv-k3u1fbpfcp-watermark.image?)

- `Props`: 父组件通过props向子组件传递数据和方法。
- `$children/$parent`: 通过parent和children访问父组件和子组件的实例。
- `refs`: 通过refs访问子组件实例。
- `$root`: 指访问根实例的方法。
- `Vuex`: 使用状态管理模式Vuex，实现全局统一管理数据，组件之间状态的共享和管理，通过全局store的方式来实现组件间的通信。
- `provide/inject`: 父组件提供数据和方法，子组件通过inject获取。
- `$emit/$on`:子组件通过$emit触发自定义事件$on，并将数据传递给父组件（可以实现非父子组件之间的通信）。
- `eventbus`: 通过事件总线EventBus，实现组件之间的通信。
- `$attrs/$listener`: vue3中已经废弃



但是在vue3中`$on`、`$children`、`$listener`已被废弃，eventbus的使用也建议引入第三方工具包[tiny-emitter](https://github.com/scottcorgan/tiny-emitter)使用。

### 根据组件之间关系讨论组件通信最为清晰有效

1、父子组件

-  `props`/`$emit`/`$parent`/`ref`/`$attrs`

2、兄弟组件
-   `$parent`/`$root`/`eventbus`/`vuex`

3、跨层级关系
-   `eventbus`/`vuex`/`provide`&`inject`

### 组件之间通信方式的用法和优缺点

**`Props通信`**
props是Vue组件的属性，父组件通过props向子组件传递数据和方法。

⚠️：在子组件中，无法直接修改父组件中的数据，因为数据是`单向流动`的，只能由父组件传递给子组件。如果需要修改数据，可以通过在子组件中触发父组件的事件或调用父组件中的方法来实现。

**`$children/$parent通信`**
在Vue中，可以使用`$children`和`$parent`来实现非[结构化的组件](https://echo-lj.github.io/2023/04/18/vue18/)通信，即在[嵌套的组件](https://echo-lj.github.io/2023/04/18/vue18/)之间进行数据传递和方法调用，同时不需要进行显式的声明和注册。

* `$children`是Vue实例的一个属性，它包含了当前实例的所有子组件。通过`$children`可以访问到所有的子组件实例，然后对子组件进行操作和访问其属性和方法。

* `$parent`是Vue实例的另一个属性，用于访问当前实例的父组件实例。通过`$parent`可以获取到父组件的实例，并调用父组件的属性和方法。

⚠️：使用`$children`和`$parent`可以实现非常灵活的组件通信，但有时也会使代码难以跟踪和调试，建议在实际开发中慎用。如果需要进行`结构化的组件`通信，在组件定义时可以使用props、自定义事件等更加清晰和明确的机制来实现。

⚠️：当父组件中有多个子组件的时候，`$children`获取到的结果是一个`数组`，包含了当前实例的所有直接子组件实例。

⚠️：当子组件中有多个父组件的时候（子组件为[动态组件](https://echo-lj.github.io/2023/04/18/vue19/)），`$parent`获取到的结果是子组件的第一个父组件。

```
通常情况下，在组件树中，一个子组件只有一个直接父组件，因此$parent只能返回它的唯一一个父组件。但是，如果出现了一个组件A有多个父组件的情况，例如在使用Vue动态组件时，可以根据渲染出的结果确定组件 A 的第一个父级组件。

如果组件A有多个父组件，并且想从一个特定的父组件获取数据或调用方法，可以考虑使用provide/inject或event bus来实现跨级组件之间的通信。
```

* 爷孙组件通信： 孙子组件通过`this.$parent.$parent.changeGrandparentMessage`触发爷组件的`changeGrandparentMessage`。


**`$refs通信`**
使用`ref属性`给组件添加一个唯一的标识符，然后在父组件中通过`$refs属性`获取子组件的引用，从而访问和控制子组件的`方法`和`属性`.

⚠️：在组件使用`refs`时，需要注意避免滥用，尽量在必要时使用，否则会增加代码耦合程度，导致代码难以维护。

**`$root通信`**
在Vue中，`$root`是Vue实例的根实例，通过`this.$root`可以获取到整个Vue应用的根实例。在Vue中，根实例是唯一的并且全局可访问的。

⚠️：滥用`$root`或者过度依赖`$root`可能会导致代码的耦合程度增加，并使得代码难以维护，因此应谨慎使用。

**`Vuex通信`**

`Vuex`是Vue.js官方提供的状态管理工具，用于在Vue.js应用程序中进行`状态管理`和`数据流控制`。它包含了一个`全局状态容器`、`改变状态的方法`和`管理方法`，通过这些功能可以简化组件之间的通信和数据的共享。

```
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.store({
    state: { 
        // 状态
    },
    mutations: { 
        // 更改状态
    },
    actions: { 
        // 异步操作状态
    },
    getters: {
        // 计算状态
    }
})

export default store
```

**`provide/inject通信`**
在Vue中，通过`provide`和`inject`可以实现跨层级组件之间的通信，即在祖先组件中提供数据，然后在需要访问该数据的子孙组件中进行注入，从而实现跨级组件通信。
缺点：在任意层级都能访问，导致数据变化追踪比较困难。

`具体使用方法如下：`

在提供数据的祖先组件中，通过`provide`提供数据：

```
export default {
  provide() {
    return {
      foo: 'bar',
      hello() {
        console.log('Hello!')
      }
    }
  },
  data() {
    return {
      message: 'Hello World'
    }
  }
}
```
在需要访问数据的子孙组件中，使用`inject`将该数据注入：
```
export default {
  inject: ['foo', 'hello'],
  data() {
    return { 
      message: 'Hello World' 
    }
  },
  methods: {
     sayHello() {
        this.hello()
     }
  }
}
```
在以上示例中，`provide`提供数据和方法，`inject`将数据和方法注入到子孙组件中来使用。

需要注意以下注意事项：

1、通过`provide`和`inject`提供和注入的数据不是响应式的，即当提供数据的祖先组件数据变化时，并不会自动更新所有注入该数据的子孙组件。

`provide`和`inject`不是响应式的，即当提供数据的祖先组件使用`this.message = 'world'`来改变提供的数据时，在子孙组件中所得到的数据是不会发生变化的(可以配合使用`Vue.observable()`来实现响应式)。

2、`provide`和`inject`并不推荐在大型组件树中使用，因为它可能引导开发者依赖更深层次的组件（父组件）的数据，这样会使应用程序难以维护。

3、为了避免名称冲突，并且使得代码更具有语义，强烈建议使用`字符串数组`形式进行注入。如：

```
export default {
  inject: ['myMessage'],
  mounted() {
    console.log(this.myMessage)
  }
}
```
总之，`provide`和`inject`是Vue中的一种特殊的API，它可以让你将数据注入到子孙组件中，但同时也需要谨慎使用。

**`$emit/$on通信`**

在Vue中，`$emit`和`$on`是两个用于组件之间通信的方法，通过组合使用这两个方法，可以实现非父子组件之间的通信。

简要地说，`$emit`用于在一个组件内发出自定义事件，而`$on`用于监听所发出的自定义事件。

⚠️： `$emit`所发出的事件会经过整个组件树，直到被某个组件的`$on`方法进行处理。因此，通过`$emit/$on`可实现非父子组件之间的通信，但同时也要注意事件名的命名规范和事件参数的传递方式。


**`EventBus通信`**
在Vue中，`EventBus`是一种单独的Vue实例，实现事件的监听和发布(发布-订阅模式)，实现组件之间的通信。

* 优点: 它允许在不涉及父子关系的组件之间轻松传递数据，因此非常适用于中小型Vue应用程序中的组件通信.
* 缺点: 使用在实际的项目操作中发现，如不能实现很好的`事件监听与发布`的管理，往往容易导致数据流的混乱，在多人协作的项目中，不利于项目的维护.


以下是`EventBus`的用法：

* 定义EventBus

在文件中定义一个 EventBus 实例：

```
import Vue from 'vue';
export const eventBus = new Vue();
```
在上面的代码中，首先引入Vue库，然后创建一个新的Vue实例，并将它导出，以便在应用程序的任何地方使用该实例。

* 监听事件

监听`EventBus`上发出的事件：

```
// 监听事件
import { eventBus } from './event-bus.js';
eventBus.$on('my-event', function (data) {
  // 处理接收到的数据
});
```
在上面的代码中，我们首先引入`eventBus`实例，然后使用`$on`方法监听名为`my-event`的事件，并定义了一个回调函数来处理该事件的数据。

* 触发事件

触发`EventBus`实例上定义的事件：

```
// 触发事件
import { eventBus } from './event-bus.js';
eventBus.$emit('my-event', data);
```
在上面的代码中，我们使用`$emit`方法在EventBus实例上触发了名为`my-event`的事件，并将数据data作为事件的参数进行了传递。

⚠️：使用`EventBus`时需要遵循一定的规则，例如`事件的命名规范`、`事件参数的传递方式`等，并且在使用`EventBus`时要防止`命名冲突`，以及避免`复杂的数据流量的交换`。

总之，`EventBus`是Vue中用于组件通信的一种简单而有效的方法，但需注意适用场景和使用规则。

**`$attrs/$listener`**
vue3中已经废弃.

在Vue中，`$attrs`和`$listeners`是两个特殊的属性，用于组件之间的传递额外的组件属性和事件监听器以及它们的回调函数。

下面是使用`$attrs`和`$listeners`的示例：

`$attrs`用于从子组件中访问父组件中的非prop属性。我们可以在父组件中使用v-bind绑定所有非prop属性：

```
<my-component v-bind="$attrs" />
```

在子组件中可以通过`$attrs`来访问这些非prop属性：

```
<template>
  <div>
    <h1>{{ title }}</h1>
    <slot />
  </div>
</template>

<script>
export default {
  props: {
    // 省略其他props...
  },
  computed: {
    title() {
      return this.$attrs.title
    }
  }
}
</script>
```

`$listeners`用于将父组件监听的事件传递给子组件，以及使用回调函数。我们可以在父组件中使用v-on添加事件监听器：

```
<my-component v-on="$listeners" @custom-event="handleCustomEvent" />
```
在子组件中可以通过`$listeners`来接收这些事件监听器和处理它们的回调函数：

```
<template>
  <button @click="$emit('custom-event')">Click me</button>
</template>

<script>
export default {
  props: {
    // 省略其他props...
  },
  methods: {
    handleClick() {
      this.$emit('custom-event')
    }
  }
}
</script>
```
在上面的代码中，`$listeners`对象包含了父组件中传递的所有事件监听器和回调函数，我们可以通过它来绑定这些事件和回调函数。

⚠️： `$attrs`和`$listeners`需要和其他属性和事件配合使用，具体使用时还需结合具体场景来选择是否需要使用。

## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️
