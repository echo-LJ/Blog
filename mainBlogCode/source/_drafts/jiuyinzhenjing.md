---
title: 九阴真经
date: 2023-04-13 15:14:09
tags: 面经
---

<meta name="referrer" content="no-referrer"/>

# 10086、快应用和轻应用的区别
# 10086 微前端：https://tech.meituan.com/2018/09/06/fe-tiny-spa.html

# created中定义a = 1，使用Vue.observable实现响应式，实现监听

在Vue中，可以使用Vue.observable()方法将一个普通的Javascript对象转化为可观察（响应式）对象，使其能够被Vue的依赖追踪系统所追踪和更新。如果在created钩子函数中定义的变量需要被监听，可以将其包装为可观察（响应式）对象，然后使用$watch方法进行监听。

示例如下：

copy code
import Vue from 'vue'

export default {
  created() {
    const state = Vue.observable({ a: 1 })
    this.$watch(() => state.a, (newValue, oldValue) => {
      console.log(`a变为 ${newValue}`)
    })
  }
}
在上面的代码中，我们通过Vue.observable()方法将一个普通Javascript对象{a: 1}转化为可观察（响应式）对象，并保存在变量state中。然后，我们使用$watch方法监听state.a属性的变化，并在变化时输出变化信息。

需要注意的是，如果要使用Vue.observable()方法包装一个对象，这个对象必须是可扩展的（Extensible）。如果对象不可扩展（Non-extensible），包装后也无法将其中的属性变为可观察对象。可以使用Object.freeze()方法将对象冻结（Frozen）以确保其不可扩展，这样就无法将其包装为可观察对象。

# 在created定义了一个变量 let a = 1 watch怎么监听a？

在Vue中，可以使用$watch方法来监听组件数据的变化，包括在created钩子函数中定义的变量。

如果要监听在created中定义的变量a的变化，可以在组件的created钩子函数中使用$watch方法：

copy code
export default {
  created() {
    let a = 1;
    this.$watch(() => this.a, (newValue, oldValue) => {
      console.log(`a变为 ${newValue}`);
    });
  }
}
在上面的代码中，我们定义了一个变量a，并使用$watch方法监听其变化。$watch方法接收两个参数，第一个参数是一个函数，返回要监听的数据值，这里使用箭头函数返回组件的数据a；第二个参数是一个回调函数，监听到数据变化时触发。在回调函数中，我们可以进行自定义的操作，例如输出变化信息。

需要注意的是，如果要在$watch中监听定义在created钩子函数中的变量，需要将变量a保存在组件实例中，例如通过this.a = 1。如果在created中直接定义变量而不挂载到组件实例上，则无法在$watch中监听其变化。

# 10086、Vue2.6+新全局API：Vue.observable()

# 2、new vue中的render的方法是什么

在Vue中，render是一个函数，用于将VNode渲染成真实的DOM节点。render函数需要返回一个VNode，它描述了将要被渲染的节点，包括节点的类型、属性、子节点以及事件等信息。Vue的渲染引擎将根据这个VNode来生成真实的DOM节点，然后将其插入到页面中。

render函数的模板如下：

copy code
createElement(tag, [data], [children])
其中，tag参数是一个字符串，用于指定要渲染的节点类型，比如'div'、'span'等；data参数是一个对象，用于指定节点的属性，比如class、style、props等；children参数是一个数组，表示当前节点的子节点。createElement函数将返回一个VNode节点。

在使用render函数时，你需要根据当前组件的状态、属性和其他数据来动态生成VNode节点。示例代码：

copy code
// myComponent.vue
export default {
  props: {
    msg: String
  },
  render(h) {
    return h('div', {
      class: 'my-component',
      style: {
        fontSize: '16px'
      }
    }, [
      h('span', 'Hello'),
      h('span', this.msg)
    ])
  }
}
在上面这个例子中，我们定义了一个名为myComponent的组件，它接收一个名为msg的属性。在render函数中，我们通过h方法生成一个div节点，并设置了该节点的class和style属性。然后，我们使用h方法创建了两个span节点，分别用于展示文本'Hello'和this.msg。最后，我们将这两个span节点作为div节点的子节点，并作为render函数的返回值返回。

当组件被渲染时，Vue将执行render函数生成对应的VNode节点，在根据这个VNode节点生成真实的DOM节点并插入到页面中。







### 高效的依赖追踪机制








**vue2和vue3中区别：** 在vue2中使用v-bind添加sync修饰符:value.sync，实现和v-model相同的作用，而vue3中废除了sync修饰符，vue3中的v-model默认相当于modelValue和@update:modelValue,vue3中甚至可以用参数形式指定多个不同的绑定，如v-model:foo和v-model:bar




# 2、子组件实例如何挂载到根vue实例上的

在Vue应用程序中，可以通过Vue实例化挂载一个根组件，然后在根组件中注册其他子组件，这些子组件将会被挂载到根组件上。例如：

copy code
// main.js
import Vue from 'vue'
import App from './App.vue'
import ChildComponent from './components/ChildComponent.vue'

Vue.component('child-component', ChildComponent)

new Vue({
  render: h => h(App)
}).$mount('#app')
在上面的代码中，我们首先引入了Vue和App组件和一个名为ChildComponent的子组件。然后，我们使用Vue.component()方法将ChildComponent注册为全局组件。

最后，我们通过new Vue()创建了一个Vue实例，并将该实例挂载到id为“app”的DOM元素上。这样，如果App组件中包含所有其他组件，那么这些组件都将被加载和挂载到根Vue实例中。

在根Vue实例中，所有组件都可以通过this关键字访问到全局的Vue实例。通过引用根Vue实例，我们可以在组件中进行跨层次通信和跨组件状态管理。








# 3、数字证书在传输过程中可以被替换吗

数字证书是一种用于验证身份和提供安全通信的电子证书，包括公钥、私钥等重要信息，被广泛应用于网络通信和电子商务等领域。数字证书的安全性与其在传输过程中是否会被替换有关。

在数字证书传输过程中，如果没有特别的安全保护措施，例如使用SSL/TLS等安全传输协议和密钥交换机制，就有可能受到中间人攻击（Man-in-the-middle attack）的威胁。中间人攻击是指攻击者能够获取数字证书并伪造与数字证书相关的信息，进而伪装成合法的通信双方进行通信，从而获取敏感信息或控制通信内容的一种攻击方式。在这种情况下，数字证书就可能被替换或篡改。

为了保证数字证书的安全性，建议采取以下措施：

使用安全的传输协议和密钥交换机制，例如SSL/TLS，以保护数字证书在传输过程中的机密性、完整性和真实性。

在信任关系建立时，对数字证书进行验证和验证，以确定证书是否有效和合法。例如，使用数字证书的公钥验证数字签名，验证发行者与证书主题等信息。

利用数字证书的吊销机制，及时吊销被攻击者篡改的证书，以减少被攻击者的损失。

综上所述，为了保障数字证书在传输过程中的安全性，需要采取综合的安全保护措施，并充分考虑数字证书在传输过程中的风险和潜在威胁。


# 5、Vue中如何扩展一个组件
**逻辑扩展：** mixins、extends、composition api

**内容扩展：** slot

- mixins：
    ```
    // 复用代码：它是一个配置对象，选项和组件里面一样
    const mymixin = {
       methods: {
          dosomething(){}
       }
    }
    // 全局混入：将混入对象传入
    Vue.mixin(mymixin)

    // 局部混入：做数组项设置到mixins选项，仅作用于当前组件
    const Comp = {
       mixins: [mymixin]
    }
    ```
- slot：
    ```
    // child.vue
    <div>
      <slot>这个内容会被父组件传递的内容替换</slot>
    </div>

    // parent.vue
    <div>
       <Child>来自老爹的内容</Child>
    </div>
    ```
- extends
    ```
    // 扩展对象
    const myextends = {
       methods: {
          dosomething(){}
       }
    }
    // 组件扩展：做数组项设置到extends选项，仅作用于当前组件
    // 跟混入的不同是它只能扩展单个对象
    // 另外如果和混入发生冲突，该选项优先级较高，优先起作用。执行顺序：extends > mixins > 组件
    const Comp = {
       extends: myextends
    }
    ```
        Vue.extend: 使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象

        let testComponent = Vue.extend({
            template:`
            <div>{{test}}</div>
            `
            data(){
                test:"hello world"
            }
        })
        let newConponet1 = new testComponent().$mount("#id1")
        let newConponet2 = new testComponent().$mount("#id2")

    
- Vue3 composition api

    混入的数据和方法**不能明确判断来源**且可能和当前组件内变量**产生命名冲突**,vue3中引入的composition api，可以很好解决这些问题，利用独立出来的响应式模块可以很方便的编写独立逻辑并提供响应式的数据，然后在setup选项中组合使用，增强代码的可读性和维护性
    ```
    // 复用逻辑1
    function useXX() {}
    // 复用逻辑2
    function useYY() {}
    // 逻辑组合
    const Comp = {
       setup() {
          const {xx} = useXX()
          const {yy} = useYY()
          return {xx, yy}
       }
    }
    ```


# 6、子组件可以直接改变父组件的数据吗

**原则：** Vue遵从one-way-data-flow单向数据流原则，所有的prop都使得其父子之间形成了一个单向下行绑定，这样是为了防止子组件意外变更父组件的状态，从而导致你的应用数据流难以理解
**解决方案：** 实践中可通过emit事件去让父组件做这个变更，或者将prop的值作为子组件数据的初始值、或者使用计算属性
```
// 定义初始值
const props = defineProps(['initialCounter'])
const counter = ref(props.initialCounter)

// 利用计算属性computed
const props = defineProps(['size'])
// prop变化，计算属性自动更新
const normalizedSize = computed(() => props.size.trim().toLowerCase())
```

# 7、动态路由

### 多个路由共用一个组件

-   很多时候，我们需要**将给定匹配模式的路由映射到同一个组件**，这种情况就需要定义动态路由。

-   例如，我们可能有一个 `User` 组件，它应该对所有用户进行渲染，但用户 ID 不同。在 Vue Router 中，我们可以在路径中使用一个动态字段来实现，例如：`{ path: '/users/:id', component: User }`，其中`:id`就是路径参数

-   *路径参数* 用冒号 `:` 表示。当一个路由被匹配时，它的 *params* 的值将在每个组件中以 `this.$route.params` 的形式暴露出来。

-   参数还可以有多个，例如`/users/:username/posts/:postId`；除了 `$route.params` 之外，`$route` 对象还公开了其他有用的信息，如 `$route.query`、`$route.hash` 等。


### 路由的权限管理

-   权限管理一般需求是**页面权限**和**按钮权限**的管理

-   具体实现的时候分后端和前端两种方案：

    前端方案会**把所有路由信息在前端配置**，通过路由守卫要求用户登录，用户**登录后根据角色过滤出路由表**。比如我会配置一个`asyncRoutes`数组，需要认证的页面在其路由的`meta`中添加一个`roles`字段，等获取用户角色之后取两者的交集，若结果不为空则说明可以访问。此过滤过程结束，剩下的路由就是该用户能访问的页面，**最后通过`router.addRoute(accessRoutes)`方式动态添加路由**即可。

    后端方案会**把所有页面路由信息存在数据库**中，用户登录的时候根据其角色**查询得到其能访问的所有页面路由信息**返回给前端，前端**再通过`addRoutes`动态添加路由**信息

    按钮权限的控制通常会**实现一个指令**，例如`v-permission`，**将按钮要求角色通过值传给v-permission指令**，在指令的`moutned`钩子中可以**判断当前用户角色和按钮是否存在交集**，有则保留按钮，无则移除按钮。

-   纯前端方案的优点是实现简单，不需要额外权限管理页面，但是维护起来问题比较大，有新的页面和角色需求就要修改前端代码重新打包部署；服务端方案就不存在这个问题，通过专门的角色和权限管理页面，配置页面和按钮权限信息到数据库，应用每次登陆时获取的都是最新的路由信息，可谓一劳永逸！

    服务端返回的路由信息如何添加到路由器中
    
    ``` 
    // 前端组件名和组件映射表
    const map = {
      //xx: require('@/views/xx.vue').default // 同步的⽅式
      xx: () => import('@/views/xx.vue') // 异步的⽅式
    }
    // 服务端返回的asyncRoutes
    const asyncRoutes = [
      { path: '/xx', component: 'xx',... }
    ]
    // 遍历asyncRoutes，将component替换为map[component]
    function mapComponent(asyncRoutes) {
      asyncRoutes.forEach(route => {
        route.component = map[route.component];
        if(route.children) {
          route.children.map(child => mapComponent(child))
        }
            })
    }
    mapComponent(asyncRoutes)
    ```

### vue-router原理

**手写router思路：**

一个SPA应用的路由需要解决的问题是**页面跳转内容改变同时不刷新**，同时路由还需要以插件形式存在，所以：

vue路由要解决的问题：用户点击跳转链接内容切换，页面不刷新。

-   借助hash或者history api实现url跳转页面不刷新
    
        ##### hash 实现
        hash 是 URL 中 hash (#) 及后面的那部分，常用作锚点在页面内进行导航，**改变 URL 中的 hash 部分不会引起页面刷新**
        通过 hashchange 事件监听 URL 的变化，改变 URL 的方式只有这几种：
        1.  通过浏览器前进后退改变 URL
        2.  通过`<a>`标签改变 URL
        3.  通过window.location改变URL

        ##### history 实现
        history 提供了 pushState 和 replaceState 两个方法，**这两个方法改变 URL 的 path 部分不会引起页面刷新**
        history 提供类似 hashchange 事件的 popstate 事件，但 popstate 事件有些不同：
        1.  通过浏览器前进后退改变 URL 时会触发 popstate 事件
        2.  通过pushState/replaceState或`<a>`标签改变 URL 不会触发 popstate 事件。
        3.  好在我们可以拦截 pushState/replaceState的调用和`<a>`标签的点击事件来检测 URL 变化
        4.  通过js 调用history的back，go，forward方法可触发该事件
        所以监听 URL 变化可以实现，只是没有 hashchange 那么方便。
        **reference：** https://juejin.cn/post/6854573222231605256#heading-2

-   同时监听hashchange事件或者popstate事件处理跳转
-   根据hash值或者state值从routes表中匹配对应component并渲染之

1.  首先我会定义一个`createRouter`函数，返回路由器实例，实例内部做几件事：

    -   保存用户传入的配置项、constructer
    -   监听hash或者popstate事件
    -   回调里根据path匹配对应路由

1.  将router定义成一个Vue插件，即实现install方法，内部做两件事：

    -   实现两个全局组件：router-link和router-view，分别实现页面跳转和内容显示
         
             router-view本质是一个容器，路由变化时清空容器内容，更新为新的路由页面
             router-link本质是一个a标签，提供声明式导航
         
    -   定义两个全局变量：$route和$router，组件内可以访问当前路由和路由器实例
  
             `$router`是VueRouter的实例对象，`$route`是当前路由对象，也就是说`$route`是`$router`的一个属性 注意每个组件添加的`$route`是是同一个，`$router`也是同一个，所有组件共享的。

 





# 9、nextTick的使用和原理

1. **(nextTick是做什么的:)** [nextTick](https://link.juejin.cn?target=https%3A%2F%2Fstaging-cn.vuejs.org%2Fapi%2Fgeneral.html%23nexttick "https://staging-cn.vuejs.org/api/general.html#nexttick")是等待下一次 DOM 更新刷新的工具方法。
2. **(为什么需要它呢:)** 当你在 Vue 中更改响应式状态时，最终的 DOM 更新并不是同步生效的，而是由 Vue 将它们缓存在一个队列中，同一事件循环中发生的所有数据变更会异步的批量更新。这样是为了确保每个组件无论发生多少状态改变，都仅执行一次更新。
3. **(开发时何时使用它:)** 开发时，有两个场景我们会用到nextTick：

-   created中想要获取DOM时；
-   响应式数据变化后获取DOM更新后的状态，比如希望获取列表更新后的高度。

4. **(下面介绍一下如何使用nextTick:)** nextTick签名如下：`function nextTick(callback?: () => void): Promise<void>`

    所以我们只需要在传入的回调函数中访问最新DOM状态即可，或者我们可以await nextTick()方法返回的Promise之后做这件事。

5. **(原理解读，结合异步更新和nextTick生效方式:)** 在Vue内部，nextTick之所以能够让我们看到DOM更新后的结果，是因为我们传入的callback会被添加到队列刷新函数(flushSchedulerQueue)的后面（放入微任务队列），这样等队列内部的更新函数都执行完毕，所有DOM操作也就结束了，callback自然能够获取到最新的DOM值。

因为js是单线程语言，当遇到异步任务(如ajax操作等)时，不可能一直等待异步完成，再继续往下执行，在这期间浏览器是空闲状态，显而易见这会导致巨大的资源浪费。

### js 异步执行的运行机制。

1.  所有任务都在主线程上执行，形成一个执行栈。
2.  主线程会不停的从执行栈中读取事件，会执行完所有栈中的同步代码
3.  当主线程遇到一个异步事件后，是会将这个事件挂在与执行栈不同的队列中，我们称之为任务队列
4.  一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"。那些对应的异步任务，结束等待状态，进入执行栈并开始执行。
5.  **主线程不断重复上面的第三步**。

### 宏任务与微任务:

异步任务分为 宏任务（macrotask） 与 微任务 (microtask)，不同的API注册的任务会依次进入自身对应的队列中，然后等待 Event Loop 将它们依次压入执行栈中执行。

**宏任务(macrotask)：** ：

script(整体代码)、setTimeout、setInterval、UI 渲染、 I/O、postMessage、 MessageChannel、setImmediate(Node.js 环境)

**微任务(microtask)：**

Promise、 MutaionObserver、process.nextTick(Node.js环境）

# 10、watch和computed的区别以及选择

1.  先看[computed](https://link.juejin.cn/?target=https%3A%2F%2Fvuejs.org%2Fapi%2Freactivity-core.html%23computed "https://vuejs.org/api/reactivity-core.html#computed"), [watch](https://link.juejin.cn/?target=https%3A%2F%2Fvuejs.org%2Fapi%2Freactivity-core.html%23watch "https://vuejs.org/api/reactivity-core.html#watch")两者定义，列举使用上的差异
1.  列举使用场景上的差异，如何选择
1.  使用细节、注意事项
1.  vue3变化

-   计算属性可以**从组件数据派生出新数据**，最常见的使用方式是设置一个函数，返回计算之后的结果，computed和methods的差异是它具备缓存性，如果依赖项不变时不会重新计算。侦听器**可以侦测某个响应式数据的变化并执行副作用**，常见用法是传递一个函数，执行副作用，watch没有返回值，但可以执行异步操作等复杂逻辑。

-   计算属性常用场景是简化行内模板中的复杂表达式，模板中出现太多逻辑会是模板变得臃肿不易维护。侦听器常用场景是状态变化之后做一些额外的DOM操作或者异步操作。选择采用何用方案时首先看是否需要派生出新值，基本能用计算属性实现的方式首选计算属性。

-   使用过程中有一些细节，比如计算属性也是可以传递对象，成为既可读又可写的计算属性。watch可以传递对象，设置deep、immediate等选项。

-   vue3中watch选项发生了一些变化，例如不再能侦测一个点操作符之外的字符串形式的表达式； reactivity API中新出现了watch、watchEffect可以完全替代目前的watch选项，且功能更加强大

## watch
`watch` 接受三个参数，第一个参数可以是不同形式的“数据源”，它可以是：

-   一个 ref
-   一个计算属性
-   一个 getter 函数（有返回值的函数）
-   一个响应式对象
-   以上类型的值组成的数组
-   
```
const x = ref(1)
const y = ref(1)
const doubleX = computed(() => x.value * 2)
const obj = reactive({ count: 0 })

// 单个 ref
watch(x, (newValue) => {
  console.log(`x is ${newValue}`)
})

// 计算属性
watch(doubleX, (newValue) => {
  console.log(`doubleX is ${newValue}`)
})

// getter 函数
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// 响应式对象
watch(obj, (newValue, oldValue) => {
  // 在嵌套的属性变更时触发
  // 注意：`newValue` 此处和 `oldValue` 是相等的
  // 因为它们是同一个对象！
})

// 以上类型的值组成的数组
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```
## watchEffect
`watchEffect` 接受两个参数，第一个参数是数据发生变化时执行的回调函数，用法和 watch 一样。第二个参数是一个可选的对象，支持 flush 和 onTrack / onTrigger 选项，功能和 watch 相同

`watchEffect` 会立即执行一遍回调函数，如果这时函数产生了副作用，Vue 会自动追踪副作用的依赖关系，自动分析出侦听数据源
```
// 一个参数就可以搞定
watchEffect(async () => {
  const response = await fetch(url.value)
  data.value = await response.json()
})
```

## `watch` vs. `watchEffect`

`watch` 和 `watchEffect` 的主要功能是相同的，都能响应式地执行回调函数。它们的区别是追踪响应式依赖的方式不同：

-   `watch` 只追踪明确定义的数据源，不会追踪在回调中访问到的东西；默认情况下，只有在数据源发生改变时才会触发回调；`watch` 可以访问侦听数据的新值和旧值。
-   `watchEffect` 会初始化执行一次，在副作用发生期间追踪依赖，自动分析出侦听数据源；`watchEffect` 无法访问侦听数据的新值和旧值。

简单一句话，`watch` 功能更加强大，而 `watchEffect` 在某些场景下更加简洁。

# 11、Vue 子组件和父组件创建和挂载顺序
-   创建过程自上而下，挂载过程自下而上；即：

    -   parent created
    -   child created
    -   child mounted
    -   parent mounted

-   之所以会这样是因为Vue创建过程是一个递归过程，先创建父组件，有子组件就会创建子组件，因此创建时先有父组件再有子组件；子组件首次创建时会添加mounted钩子到队列，等到patch结束再执行它们，可见子组件的mounted钩子是先进入到队列中的，因此等到patch结束执行这些钩子时也先执行。

  

# 11、从0到1自己构架一个vue项目
1.  构建项目，创建项目基本结构
2.  引入必要的插件：
3.  代码规范：prettier，eslint
4.  提交规范：husky，lint-staged
5.  其他常用：svg-loader，vueuse，nprogress
6.  常见目录结构

1.  从0创建一个项目我大致会做以下事情：项目构建、引入必要插件、代码规范、提交规范、常用库和组件
2.  目前vue3项目我会用vite或者create-vue创建项目
3.  接下来引入必要插件：路由插件vue-router、状态管理vuex/pinia、ui库我比较喜欢element-plus和antd-vue、http工具我会选axios
4.  其他比较常用的库有vueuse，nprogress，图标可以使用vite-svg-loader
5.  下面是代码规范：结合prettier和eslint即可
6.  最后是提交规范，可以使用husky，lint-staged，commitlint

* * *

7.  目录结构我有如下习惯： `.vscode`：用来放项目中的 vscode 配置

    `plugins`：用来放 vite 插件的 plugin 配置

    `public`：用来放一些诸如 页头icon 之类的公共文件，会被打包到dist根目录下

    `src`：用来放项目代码文件

    `api`：用来放http的一些接口配置

    `assets`：用来放一些 CSS 之类的静态资源

    `components`：用来放项目通用组件

    `layout`：用来放项目的布局

    `router`：用来放项目的路由配置

    `store`：用来放状态管理Pinia的配置

    `utils`：用来放项目中的工具方法类

    `views`：用来放项目的页面文件


# 11、说说你对虚拟 DOM 的理解

1.  虚拟dom顾名思义就是虚拟的dom对象，它本身就是一个 `JavaScript` 对象，只不过它是通过不同的属性去描述一个视图结构。

1.  通过引入vdom我们可以获得如下好处：

    **将真实元素节点抽象成 VNode，有效减少直接操作 dom 次数，从而提高程序性能**

    -   直接操作 dom 是有限制的，比如：diff、clone 等操作，一个真实元素上有许多的内容，如果直接对其进行 diff 操作，会去额外 diff 一些没有必要的内容；同样的，如果需要进行 clone 那么需要将其全部内容进行复制，这也是没必要的。但是，如果将这些操作转移到 JavaScript 对象上，那么就会变得简单了。
    -   操作 dom 是比较昂贵的操作，频繁的dom操作容易引起页面的重绘和回流，但是通过抽象 VNode 进行中间处理，可以有效减少直接操作dom的次数，从而减少页面重绘和回流。
            
重绘&回流

```
浏览器使用流式布局模型 (Flow Based Layout)。
1.  浏览器会把`HTML`解析成`DOM`，把`CSS`解析成`CSSOM`，`DOM`和`CSSOM`合并就产生了`Render Tree`。
2.  有了`RenderTree`，我们就知道了所有节点的样式，然后计算他们在页面上的大小和位置，最后把节点绘制到页面上。
3.  由于浏览器使用流式布局，对`Render Tree`的计算通常只需要遍历一次就可以完成，但`table`及其内部元素除外，他们可能需要多次计算，通常要花3倍于同等元素的时间，这也是为什么要避免使用`table`布局的原因之一。

**一句话：回流必将引起重绘，重绘不一定会引起回流。**

回流： 当`Render Tree`中部分或全部元素的尺寸、结构、或某些属性发生改变时，浏览器重新渲染部分或全部文档的过程。
重绘： 当页面中元素样式的改变并不影响它在文档流中的位置时（例如：`color`、`background-color`、`visibility`等），浏览器会将新样式赋予给元素并重新绘制它。

### CSS

-   避免使用`table`布局。
-   尽可能在`DOM`树的最末端改变`class`。
-   避免设置多层内联样式。
-   将动画效果应用到`position`属性为`absolute`或`fixed`的元素上。
-   避免使用`CSS`表达式（例如：`calc()`）。

### JavaScript

-   避免频繁操作样式，最好一次性重写`style`属性，或者将样式列表定义为`class`并一次性更改`class`属性。
-   避免频繁操作`DOM`，创建一个`documentFragment`，在它上面应用所有`DOM操作`，最后再把它添加到文档中。
-   也可以先为元素设置`display: none`，操作结束后再把它显示出来。因为在`display`属性为`none`的元素上进行的`DOM`操作不会引发回流和重绘。
-   避免频繁读取会引发回流/重绘的属性，如果确实需要多次使用，就用一个变量缓存起来。
-   对具有复杂动画的元素使用绝对定位，使它脱离文档流，否则会引起父元素及后续元素频繁回流。

```

**方便实现跨平台**

   -   同一 VNode 节点可以渲染成不同平台上的对应的内容，比如：渲染在浏览器是 dom 元素节点，渲染在 Native( iOS、Android) 变为对应的控件、可以实现 SSR 、渲染到 WebGL 中等等
   -   Vue3 中允许开发者基于 VNode 实现自定义渲染器（renderer），以便于针对不同平台进行渲染。

* * *

3.  vdom如何生成？在vue中我们常常会为组件编写模板 - template， 这个模板会被编译器 - compiler编译为渲染函数，在接下来的挂载（mount）过程中会调用render函数，返回的对象就是虚拟dom。但它们还不是真正的dom，所以会在后续的patch过程中进一步转化为dom。

    ![image-20220209153820845](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6436e6f8fe1c4a1daa5badd187ace8dc~tplv-k3u1fbpfcp-zoom-1.image)

3.  挂载过程结束后，vue程序进入更新流程。如果某些响应式数据发生变化，将会引起组件重新render，此时就会生成新的vdom，和上一次的渲染结果diff就能得到变化的地方，从而转换为最小量的dom操作，高效更新视图。


#### 模板编译
Vue的模板编译就是将“HTML”模板编译成render函数的过程。这个过程大致可以分成三个阶段：

- 解析阶段：将“HTML”模板解析成AST语法树；
- 优化阶段：从AST语法树中找出静态子树并进行标记（被标记的静态子树在虚拟dom比对时会被忽略，从而提高虚拟dom比对的性能）；
- 代码生成阶段：通过AST生成代码字符串，并最终生成render函数。



# 11、Vue实例挂载的过程中发生了什么

### 分析

挂载过程完成了最重要的两件事：

1.  初始化
1.  建立更新机制

把这两件事说清楚即可！

### 回答范例

1.  挂载过程指的是app.mount()过程，这个过程中整体上做了两件事：**初始化**和**建立更新机制**
1.  初始化会创建组件实例、初始化组件状态，创建各种响应式数据
1.  建立更新机制（watcher）这一步会立即执行一次组件更新函数render，这会首次执行组件渲染函数并执行patch将前面获得vnode转换为dom；同时首次执行渲染函数会创建它内部响应式数据之间和组件更新函数之间的依赖关系，这使得以后数据变化时会执行对应的更新函数。

### 可能的追问

1.  响应式数据怎么创建 proxy defineProperty
1.  依赖关系如何建立  wacther-建立data与render函数的关系

    **初始化**：Vue 通过 `defineProperty或proxy` 完成了 Data 中所有数据的代理，当数据触发 get 查询时，会将当前的 Watcher 对象加入到依赖收集池 Dep 中，当数据 Data 变化时，会触发 set 通知所有使用到这个 Data 的 Watcher 对象去 update 视图
    
    **更新**：在 Data 变化时，会调用 Dep.notify 方法，通知所有订阅该data的watcher，调用 Watcher 内部的 update 方法，此方法会将所有使用到这个 Data 的 Watcher 加入一个队列，并开启一个异步队列进行更新，最终执行 `_render` 方法完成页面更新

https://zhuanlan.zhihu.com/p/168768245

# 11、Vue组件为什么只能有一个根元素

-   `vue2`中组件确实只能有一个根，但`vue3`中组件已经可以多根节点了。

-   之所以需要这样是因为`vdom`是一颗单根树形结构，`patch`方法在遍历的时候从根节点开始遍历，它要求只有一个根节点。组件也会转换为一个`vdom`，自然应该满足这个要求。

-   `vue3`中之所以可以写多个根节点，是因为引入了`Fragment`的概念，这是一个抽象的节点，如果发现组件是多根的，就创建一个Fragment节点，把多个根节点作为它的children。将来patch的时候，如果发现是一个Fragment节点，则直接遍历children创建或更新。

  

# 11、SPA、SSR

-   SPA（Single Page Application）即**单页面应用**。一般也称为 **客户端渲染**（Client Side Render）， 简称 CSR。SSR（Server Side Render）即 **服务端渲染**。一般也称为 **多页面应用**（Mulpile Page Application），简称 MPA。

-   SPA应用只会首次请求html文件，后续只需要请求JSON数据即可，因此用户体验更好，节约流量，服务端压力也较小。但是首屏加载的时间会变长，而且SEO不友好。为了解决以上缺点，就有了SSR方案，由于HTML内容在服务器一次性生成出来，首屏加载快，搜索引擎也可以很方便的抓取页面信息。但同时SSR方案也会有性能，开发受限等问题。

-   在选择上，如果我们的应用存在首屏加载优化需求，SEO需求时，就可以考虑SSR。

-   但并不是只有这一种替代方案，比如对一些不常变化的静态网站，SSR反而浪费资源，我们可以考虑[预渲染](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fchrisvfritz%2Fprerender-spa-plugin "https://github.com/chrisvfritz/prerender-spa-plugin")（prerender）方案。另外nuxt.js/next.js中给我们提供了SSG（Static Site Generate）静态网站生成方案也是很好的静态站点解决方案，结合一些CI手段，可以起到很好的优化效果，且能节约服务器资源。

内容生成上的区别：

SSR

![ss](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c27402e12a0430383f2f71fc71d871f~tplv-k3u1fbpfcp-zoom-1.image)

* * *

SPA

![sp](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4013ba346f0f44a095e0e2dd29d0426a~tplv-k3u1fbpfcp-zoom-1.image)

# 11、自定义指令

https://juejin.cn/post/7049233225708732429#heading-25

-   Vue有一组默认指令，比如`v-mode`或`v-for`，同时Vue也允许用户注册自定义指令来扩展Vue能力

-   自定义指令主要完成一些可复用低层级DOM操作

-   使用自定义指令分为定义、注册（实现install方法）和使用三步：

    -   定义自定义指令有两种方式：对象和函数形式，前者类似组件定义，有各种生命周期；后者只会在mounted和updated时执行
    -   注册自定义指令类似组件，可以使用app.directive()全局注册，使用{directives:{xxx}}局部注册
    -   使用时在注册名称前加上v-即可，比如v-focus

-   我在项目中常用到一些自定义指令，例如：

    -   复制粘贴 v-copy
    -   长按 v-longpress
    -   防抖 v-debounce
    -   图片懒加载 v-lazy
    -   按钮权限 v-premission
    -   页面水印 v-waterMarker
    -   拖拽指令 v-draggable

-   vue3中指令定义发生了比较大的变化，主要是钩子的名称保持和组件一致，这样开发人员容易记忆，不易犯错。另外在v3.2之后，可以在setup中以一个小写v开头方便的定义自定义指令，更简单了！



截止到`Vue3.2`，`Vue`一共有`16`个自带指令，包括了：

`v-text`、`v-html`、`v-show`、`v-if`、`v-else`、`v-else-if`、`v-for`、`v-on`、`v-bind`、`v-model`、`v-slot`、`v-pre`（`v-pre`指令用于跳过这个元素及其子元素的编译过程）、`v-cloak`（指令主要用于解决插值表达式在页面闪烁问题，编译后显示）、`v-once`、`v-memo`、`v-is`，其中`v-memo`是`3.2`新增的，`v-is`在`3.1.0`中废弃

```
import store from '@/store'

function checkPermission(el, binding) {
  const { value } = binding
  const roles = store.getters && store.getters.roles

  if (value && value instanceof Array) {
    if (value.length > 0) {
      const permissionRoles = value

      const hasPermission = roles.some(role => {
        return permissionRoles.includes(role)
      })

      if (!hasPermission) {
        el.parentNode && el.parentNode.removeChild(el)
      }
    }
  } else {
    throw new Error(`need roles! Like v-permission="['admin','editor']"`)
  }
}

export default {
  inserted(el, binding) {
    checkPermission(el, binding)
  },
  update(el, binding) {
    checkPermission(el, binding)
  }
}

```

```
import permission from './permission'

const install = function(Vue) {
  Vue.directive('permission', permission)
}
if (window.Vue) {
  window['permission'] = permission
  Vue.use(install); // eslint-disable-line
}
permission.install = install
export default permission
```





# 11、vuex
### 思路

1.  给定义
1.  必要性阐述
1.  何时使用
1.  拓展：一些个人思考、实践经验等

* * *

### 范例

1.  Vuex 是一个专为 Vue.js 应用开发的**状态管理模式 + 库**。它采用集中式存储，管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
1.  我们期待以一种简单的“单向数据流”的方式管理应用，即状态state -> 视图view -> 操作action单向循环的方式。但当我们的应用遇到**多个组件共享状态**时，比如：多个视图依赖于同一状态或者来自不同视图的行为需要变更同一状态。此时单向数据流的简洁性很容易被破坏。因此，我们有必要把组件的共享状态抽取出来，以一个全局单例模式管理。通过定义和隔离状态管理中的各种概念并通过强制规则维持视图和状态间的独立性，我们的代码将会变得更结构化且易维护。这是vuex存在的必要性，它和react生态中的redux之类是一个概念。
1.  Vuex 解决状态管理的同时引入了不少概念：例如state、mutation、action等，是否需要引入还需要根据应用的实际情况衡量一下：如果不打算开发大型单页应用，使用 Vuex 反而是繁琐冗余的，一个简单的 [store 模式](https://link.juejin.cn?target=https%3A%2F%2Fv3.cn.vuejs.org%2Fguide%2Fstate-management.html%23%25E4%25BB%258E%25E9%259B%25B6%25E6%2589%2593%25E9%2580%25A0%25E7%25AE%2580%25E5%258D%2595%25E7%258A%25B6%25E6%2580%2581%25E7%25AE%25A1%25E7%2590%2586 "https://v3.cn.vuejs.org/guide/state-management.html#%E4%BB%8E%E9%9B%B6%E6%89%93%E9%80%A0%E7%AE%80%E5%8D%95%E7%8A%B6%E6%80%81%E7%AE%A1%E7%90%86")就足够了。但是，如果要构建一个中大型单页应用，Vuex 基本是标配。


##### action和mutation区别 
https://zhuanlan.zhihu.com/p/548571525

vuex中的state数据不允许直接赋值修改，所以vuex创建了mutations用于定义方法来修改state中的数据，但是只能同步修改。如果异步修改会造成调试工具跟实际数据不对应，所以vuex又提供了actions，用于异步触发mutations中的方法。

总结：mutations中的方法可以直接修改state数据，actions是异步执行mutations中的方法，所以它俩都可以改数据，区别是一个是同步一个是异步。

1、流程顺序  
“相应视图—>修改State”拆分成两部分，视图触发Action，Action再触发Mutation。

2、角色定位  
基于流程顺序，二者扮演不同的角色。  
Mutation：专注于修改State，理论上是修改State的唯一途径。  
Action：异步请求。

3、限制  
Mutation：必须同步执行。使用**this.$store.commit**来提交mutation对store中的状态进行修改。

Action：提交的是 mutation，而不是直接变更状态 (非状态！！！)，可以异步，但不能直接操作State。使用**this.$store.dispatch**来提交

#### module
```
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}
const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}
const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
store.getters.c // -> moduleA里的getters
store.commit('d') // -> 能同时触发子模块中同名mutation
store.dispatch('e') // -> 能同时触发子模块中同名action
```
-   用过module，项目规模变大之后，单独一个store对象会过于庞大臃肿，通过模块方式可以拆分开来便于维护

-   可以按之前规则单独编写子模块代码，然后在主文件中通过`modules`选项组织起来：`createStore({modules:{...}})`

-   不过使用时要注意访问子模块状态时需要加上注册时模块名：`store.state.a.xxx`，但同时`getters`、`mutations`和`actions`又在全局空间中，使用方式和之前一样。如果要做到完全拆分，需要在子模块加上`namespace`选项，此时再访问它们就要加上命名空间前缀。

-   很显然，模块的方式可以拆分代码，但是缺点也很明显，就是使用起来比较繁琐复杂，容易出错。而且类型系统支持很差，不能给我们带来帮助。pinia显然在这方面有了很大改进，是时候切换过去了。

#### 手写vuex

0.  官方说`vuex`是一个状态管理模式和库，并确保这些状态以可预期的方式变更。可见要实现一个`vuex`：

    -   要实现一个`Store`存储全局状态
    -   要提供修改状态所需API：`commit(type, payload)`, `dispatch(type, payload)`

0.  实现`Store`时，可以定义Store类，构造函数接收选项options，设置属性state对外暴露状态，提供commit和dispatch修改属性state。这里需要设置state为响应式对象，同时将Store定义为一个Vue插件。

0.  `commit(type, payload)`方法中可以获取用户传入`mutations`并执行它，这样可以按用户提供的方法修改状态。 `dispatch(type, payload)`类似，但需要注意它可能是异步的，需要返回一个Promise给用户以处理异步结果。




# 11、vue3新特性

-   Composition API
-   SFC Composition API语法糖
-   Teleport传送门
-   Fragments片段
-   Emits选项
-   自定义渲染器
-   SFC CSS变量
-   Suspense


1.  api层面Vue3新特性主要包括：Composition API、SFC Composition API语法糖、Teleport传送门、Fragments 片段、Emits选项、自定义渲染器、SFC CSS变量、Suspense
1.  另外，Vue3.0在框架层面也有很多亮眼的改进：

-   更快

    -   虚拟DOM重写
    -   编译器优化：静态提升、patchFlags、block等
    -   基于Proxy的响应式系统

-   更小：更好的摇树优化，许多`Vue`的`API`可以被`Tree-Shaking`，因为使用了`es6module`，`tree-shaking` 依赖于 `es6`模块的静态结构特性

-   更容易维护：TypeScript + 模块化

-   更容易扩展

    -   独立的响应化模块
    -   自定义渲染器

https://juejin.cn/post/7098575243240800286#heading-69

1. v-model支持多个参数，支持自定义组件，vue3废除了sync
2. composition api/setUp，模块化
3. proxy实现响应式，proxy 是针对整个对象层面的代理拦截，而非 defineProperty 针对属性层面做的劫持，reactive、shallowReactive、ref、shallowRef
4. teleport，把组件传送在指定dom结构下
5. 组件不再限制只能存在一个根元素fragments
6. data 选项，都统一改成函数形式（之前根组件是对象，子组件是函数）
7. Suspense（实验性），更优雅的v-if/v-else，#default和#fallback两个插槽
8. diff算法优化，新增patchFlags，block
9. emits选项用于声明由组件触发的自定义事件
10. css变量，style支持 v-bind函数将css参数链接到组件变量


# 11、Vue响应式的理解

https://juejin.cn/post/7124973052659499038

https://zhuanlan.zhihu.com/p/168768245

1.  所谓数据响应式就是**能够使数据变化可以被检测并对这种变化做出响应的机制**。
1.  MVVM框架中要解决的一个核心问题是连接数据层和视图层，通过**数据驱动**应用，数据变化，视图更新，要做到这点的就需要对数据做响应式处理，这样一旦数据发生变化就可以立即做出更新处理。
1.  以vue为例说明，通过数据响应式加上虚拟DOM和patch算法，开发人员只需要操作数据，关心业务，完全不用接触繁琐的DOM操作，从而大大提升开发效率，降低开发难度。
1.  vue2中的数据响应式会根据数据类型来做不同处理，如果是**对象则采用Object.defineProperty()**的方式定义数据拦截，当数据被访问或发生变化时，我们感知并作出响应；如果是**数组则通过覆盖数组对象原型的7个变更方法**，使这些方法可以额外的做更新通知，从而作出响应。这种机制很好的解决了数据响应化的问题，但在实际使用中也存在一些缺点：比如初始化时的递归遍历会造成性能损失；新增或删除属性时需要用户使用Vue.set/delete这样特殊的api才能生效；对于es6中新产生的Map、Set这些数据结构不支持等问题。
1.  为了解决这些问题，vue3重新编写了这一部分的实现：利用ES6的Proxy代理要响应化的数据，它有很多好处，编程体验是一致的，不需要使用特殊api，初始化性能和内存消耗都得到了大幅改善；另外由于响应化的实现代码抽取为独立的reactivity包，使得我们可以更灵活的使用它，第三方的扩展开发起来更加灵活了。


### vue2 Object.defineProperty
    直接在一个对象上定义一个新属性(get,set)，或者修改一个对象的现有属性，并返回此对象
    
### vue3 Proxy
    主要用于改变对象的默认访问行为，实际上是在访问对象之前增加一层拦截，在任何对对象的访问行为都会通过这层拦截
    
区别 ：

1.  Proxy 是对整个对象的代理，而 Object.defineProperty 只能代理某个属性
2.  对象上新增属性和数组新增修改，Proxy可以监听到，Object.defineProperty不能（Vue2中）
3.  若对象内部属性要全部递归代理，Proxy可以只在调用时递归，而Object.defineProperty需要一次性完成所有递归，性能比Proxy差（假如对象嵌套的层级比较深的话，每一次都需要循环遍历，采用递归代理）
4.  Proxy不兼容IE，Object.defineProperty不兼容IE8及以下
5.  如果Object.defineProperty遍历到对象不存在的属性的时候，它是检测不到变化的


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/922b02dc5e1f4367b49d2d439ccb9cad~tplv-k3u1fbpfcp-watermark.image?)

***

**Vue2 部分**

Vue2 是通过 Object.defineProperty 将对象的属性转换成 getter/setter 的形式来进行监听它们的变化，当读取属性值的时候会触发 getter 进行依赖收集，当设置对象属性值的时候会触发 setter 进行向相关依赖发送通知，从而进行相关操作。

由于 Object.defineProperty 只对属性 key 进行监听，无法对引用对象进行监听，所以在 Vue2 中创建一个了 Observer 类对整个对象的依赖进行管理，当对响应式对象进行新增或者删除则由响应式对象中的 dep 通知相关依赖进行更新操作。

Object.defineProperty 也可以实现对数组的监听的，但因为性能的原因 Vue2 放弃了这种方案，改由重写数组原型对象上的 7 个能操作数组内容的变更的方法，从而实现对数组的响应式监听。

**Vue3 部分**

Vue3 则是通过 Proxy 对数据实现 getter/setter 代理，从而实现响应式数据，然后在副作用函数中读取响应式数据的时候，就会触发 Proxy 的 getter，在 getter 里面把对当前的副作用函数保存起来，将来对应响应式数据发生更改的话，则把之前保存起来的副作用函数取出来执行。

Vue3 对数组实现代理时，用于代理普通对象的大部分代码可以继续使用，但由于对数组的操作与对普通对象的操作存在很多的不同，那么也需要对这些不同的操作实现正确的响应式联系或触发响应。这就需要对数组原型上的一些方法进行重写。

比如通过索引为数组设置新的元素，可能会隐式地修改数组的 length 属性的值。同时如果修改数组的 length 属性的值，也可能会间接影响数组中的已有元素。另外用户通过 includes、indexOf 以及 lastIndexOf 等对数组元素进行查找时，可能是使用代理对象进行查找，也有可能使用原始值进行查找，所以我们就需要重写这些数组的查找方法，从而实现用户的需求。原理很简单，当用户使用这些方法查找元素时，先去响应式对象中查找，如果没找到，则再去原始值中查找。

另外如果使用 push、pop、shift、unshift、splice 这些方法操作响应式数组对象时会间接读取和设置数组的 length 属性，所以我们也需要对这些数组的原型方法进行重新，让当使用这些方法间接读取 length 属性时禁止进行依赖追踪，这样就可以断开 length 属性与副作用函数之间的响应式联系了。

链接：https://juejin.cn/post/7124351370521477128  





# 11、页面卡顿

**如果页面卡顿，你觉得可能是什么原因造成的？有什么办法锁定原因并解决吗？**

这是一个非常宽泛而又有深度的问题，他涉及到很多的页面性能优化问题，我依稀还记得当初面试被问到这个问题时我是这么回答的：

1.  先会检查是否是网络请求太多，导致数据返回较慢，可以适当做一些缓存
1.  也有可能是某块资源的bundle太大，可以考虑拆分一下
1.  然后排查一下js代码，是不是某处有过多循环导致占用主线程时间过长
1.  浏览器某帧渲染的东西太多，导致的卡顿
1.  在页面渲染过程中，可能有很多重复的重排重绘
1.  emmmmmm....不知道了

后来了解到了，感官上的长时间运行页面卡顿也有可能是因为**内存泄漏**引起的

那么到底有哪些情况会出现**内存泄漏**的情况呢？这里列举了常见的几种：

1.  闭包使用不当引起内存泄漏
1.  全局变量
1.  分离的DOM节点
1.  控制台的打印
1.  遗忘的定时器

作者：零一01  
链接：https://juejin.cn/post/6947841638118998029  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

# 11、前端模块化

#### 总结：
-   CommonJS规范主要用于服务端编程，加载模块是同步的，这并不适合在浏览器环境，因为同步意味着阻塞加载，浏览器资源是异步加载的，因此有了AMD CMD解决方案。

-   AMD规范在浏览器环境中异步加载模块，而且可以并行加载多个模块。不过，AMD规范开发成本高，代码的阅读和书写比较困难，模块定义方式的语义不顺畅。

-   CMD规范与AMD规范很相似，都用于浏览器编程，依赖就近，延迟执行，可以很容易在Node.js中运行。不过，依赖SPM 打包，模块的加载逻辑偏重

-   **ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案**

#### 模块化的定义
- 将一个复杂的程序依照一定的规则封装成几个块，并且组合在一起
- 块的内部数据和实现是私有的，只是向外部暴露一些借口与外部其它模块通信

#### 模块化的好处
-   避免命名冲突(减少命名空间污染)
-   更好的分离, 按需加载
-   更高复用性
-   高可维护性

#### 模块化规范

##### CommonJS
1.概述

- 主要应用于Node服务端，因为模块文件一般存于本地硬盘，加载较快，不用考虑非同步加载方式
    - 服务端模块的加载是在运行时同步加载的；浏览器端模块需要提前编译打包处理

2.特点
-   所有代码都运行在模块作用域，不会污染全局作用域。
-   模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
-   模块加载的顺序，按照其在代码中出现的顺序。

3.语法
-   暴露模块：`module.exports = value`或`exports.xxx = value`
-   引入模块：`require(xxx)`,如果是第三方模块，xxx为模块名；如果是自定义模块，xxx为模块文件路径
    - CommonJS暴露的模块到底是什么：加载某个模块，其实是加载该模块的module.exports属性

4.模块的加载机制
- 输出值是模块值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值
- 同步加载的，只有加载完成才能执行后面的操作

##### AMD

1.概述
- 主要应用于浏览器端，采用非同步加载，允许指定回调函数
- RequireJS，遵循AMD规范的客户端模块管理工具库

2.语法
```
// dataService.js文件
// 定义没有依赖的模块
define(function() {
  let msg = 'www.baidu.com'
  function getMsg() {
    return msg.toUpperCase()
  }
  return { getMsg } // 暴露模块
})
```

```
// alerter.js文件
define(['dataService', 'jquery'], function(dataService, $) {
  let name = 'Tom'
  function showMsg() {
    alert(dataService.getMsg() + ', ' + name)
  }
  $('body').css('background', 'green')
  // 暴露模块
  return { showMsg }
})
```

```
// main.js文件
(function() {
  require.config({
    baseUrl: 'js/', //基本路径 出发点在根目录下
    paths: {
      //自定义模块
      alerter: './modules/alerter', //此处不能写成alerter.js,会报错
      dataService: './modules/dataService',
      // 第三方库模块
      jquery: './libs/jquery-1.10.1' //注意：写成jQuery会报错
    }
  })
  require(['alerter'], function(alerter) {
    alerter.showMsg()
  })
})()
```

```
// index.html文件
<!DOCTYPE html>
<html>
  <head>
    <title>Modular Demo</title>
  </head>
  <body>
    <!-- 引入require.js并指定js主文件的入口 -->
    <script data-main="js/main" src="js/libs/require.js"></script>
  </body>
</html>
```

##### CMD

1.概述
- 浏览器端，异步加载
- 整合了CommonJS和AMD规范的特点
- Sea.js,遵循CMD规范的模块管理工具库

2.语法
**定义暴露模块：**

```
//定义没有依赖的模块
define(function(require, exports, module){
  exports.xxx = value
  module.exports = value
})
复制代码
```

```
//定义有依赖的模块
define(function(require, exports, module){
  //引入依赖模块(同步)
  var module2 = require('./module2')
  //引入依赖模块(异步)
    require.async('./module3', function (m3) {
    })
  //暴露模块
  exports.xxx = value
})
复制代码
```

**引入使用模块：**

```
define(function (require) {
  var m1 = require('./module1')
  var m4 = require('./module4')
  m1.show()
  m4.show()
})
```
##### ES6

1.概述
ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西

2.语法
- export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。
    - ES6 模块与 CommonJS 模块的差异：
        - CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。

        - CommonJS 模块是运行时加载（因为 CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成），ES6 模块是编译时输出接口（ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成）。

        - ES6 模块是动态引用，并且不会缓存值，模块里面的变量绑定其所在的模块

链接：https://juejin.cn/post/6844903744518389768  


# 11、自我介绍

面试官您好，我叫xxx，xx年毕业于xx大学，自毕业以来一直从事着前端开发的相关工作。

我擅长的技术栈是 vue 全家桶，对 vue2 和 vue3 在使用上和源码都有一定程度的钻研；打包工具对 webpack 和 vite 都比较熟悉；有从零到一主导中大型项目落地的经验和能力。

在上家公司主要是xx产品线负责人的角色，主要职责是。。。。。。

除了开发相关工作，还有一定的技术管理经验：比如担任需求评审、UI/UE交互评审评委，负责开发排期、成员协作、对成员代码进行review、组织例会等等

平常会在自己搭建的博客上记录一些学习文章或者学习笔记，也会写一些原创的技术文章发表到掘金上，获得过xx奖。


### 如何带领团队的

我在上家公司是一个技术管理的角色。

0、**落实开发规范**，我在公司内部 wiki 上有发过，从命名、最佳实践到各种工具库的使用。新人进来前期我会优先跟进他们的代码质量

1、**团队分工**：每个人单独负责一个产品的开发，然后公共模块一般我会指定某几个人开发

2、**代码质量保证**：每周会review他们的代码，也会组织交叉 review 代码，将修改结果输出文章放到 wiki中

3、**组织例会**：每周组织例会同步各自进度和风险，根据各自的进度调配工作任务

4、**技术分享**：还会组织不定时的技术分享。一开始就是单纯的我做分享，比如微前端的体系，ice stark 的源码

5、**公共需求池**：比如webpack5/vite的升级；vue2.7的升级引入setup语法糖；pnpm的使用；拓扑图性能优化

6、**优化专项**：在第一版产品出来之后，我还发起过性能优化专项，首屏加载性能，打包体积优化；让每个人去负责对应的优化项

  


作者：流年丶风尘  
链接：https://juejin.cn/post/7173316141161381924  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


# 11、原型&原型链

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/80ef455cf5ee42f18c74ced53e44263e~tplv-k3u1fbpfcp-zoom-1.image)

**原型关系：**

-   每个 class都有显示原型 prototype
-   每个实例都有隐式原型 _ proto_
-   实例的_ proto_指向对应 class 的 prototype

#### 什么是原型/原型链？

原型的本质就是一个**对象**。

当我们在创建一个构造函数之后，这个函数会默认带上一个`prototype`属性，而这个属性的值就指向这个函数的原型对象。

这个原型对象是用来为通过该构造函数创建的实例对象提供共享属性，即**用来实现基于原型的继承和属性的共享**

所以我们通过构造函数**创建的实例对象**都会从这个函数的原型对象上继承上面具有的属性

当读取实例的属性时，如果找不到，就会查找与对象关联的原型中的属性，如果还查不到，就去找原型的原型，一直找到最顶层为止（最顶层就是`Object.prototype`的原型，值为null）。

所以**通过原型一层层相互关联的链状结构就称为原型链**。


# 11、XSS / CSRF

#### XSS 和 CSRF

**xss基本概念**

Xss (Cross site scripting)跨站脚本攻击，为了和 css 区别开来所以叫 xss

Xss 指黑客向 html 或 dom 中注入恶意脚本，从而在用户浏览页面的时候利用注入脚本对用户实施攻击的手段

恶意脚本可以做到：窃取 cookie 信息、监听用户行为（比如表单的输入）、修改DOM（比如伪造登录界面骗用户输入账号密码）、在页面生成浮窗广告等

恶意脚本注入方式：

1.  存储型 xss

    黑客利用站点漏洞将恶意 js 代码提交到站点服务器，用户访问页面就会导致恶意脚本获取用户的cookie等信息。

1.  反射性 xss

    用户将一段恶意代码请求提交给 web 服务器，web 服务器接收到请求后将恶意代码反射到浏览器端

1.  基于 DOM 的 xss 攻击

    通过网络劫持在页面传输过程中更改 HTML 内容

前两种属于服务端漏洞，最后一种属于前端漏洞

**防止xss攻击的策略**

1、服务器对输入脚本进行过滤或者转码，比如将`code:<script>alert('你被xss攻击了')</script>`转换成`code:&lt;script&gt;alert(&#39;你被xss攻击了&#39;)&lt;/script&gt;`

2、充分利用内容安全策略 CSP(content-security-policy)，可以通过 http 头信息的 content-security-policy 字段控制可以加载和执行的外部资源；或者通过html的meta 标签`<meta http-equiv="Content-Security-Policy" content="script-src 'self'; object-src 'none'; style-src cdn.example.org third-party.org; child-src https:">`

3、cookie设置为 http-only, cookie 就无法通过 `document.cookie` 来读取

**csrf基本概念**

Csrf（cross site request forgery）跨站请求伪造，指黑客引导用户访问黑客的网站。

CSRF 是指黑客引诱用户打开黑客的网站，在黑客的网站中，利用用户的登录状态发起的跨站请求。简单来讲，CSRF 攻击就是黑客利用了用户的登录状态，并通过第三方的站点来做一些坏事。

**Csrf 攻击场景**

1.  自动发起 get 请求

    比如黑客网站有个图片:

    ```
    <img src="https://time.geekbang.org/sendcoin?user=hacker&number=100">
    复制代码
    ```

    黑客将转账的请求接口隐藏在 img 标签内，欺骗浏览器这是一张图片资源。当该页面被加载时，浏览器会自动发起 img 的资源请求，如果服务器没有对该请求做判断的话，那么服务器就会认为该请求是一个转账请求，于是用户账户上的 100 极客币就被转移到黑客的账户上去了。

1.  自动发起 post 请求

    黑客在页面中构建一个隐藏的表单，当用户点开链接后，表单自动提交

1.  引诱用户点击链接

    比如页面上放了一张美女图片，下面放了图片下载地址，而这个下载地址实际上是黑客用来转账的接口，一旦用户点击了这个链接，那么他的极客币就被转到黑客账户上了

**防止csrf方法**

1、设置 cookie 时带上SameSite: strict/Lax选项

2、验证请求的来源站点，通过 origin 和 refere 判断来源站点信息

3、csrf token，浏览器发起请求服务器生成csrf token，发起请求前会验证 csrf token是否合法。第三方网站肯定是拿不到这个token。我们的 csrf token 是前后端约定好后写死的。

####

  

# 11、闭包
闭包是指有权访问另一个函数作用域中的变量的函数

**稍全面的回答**： 在js中变量的作用域属于函数作用域, 在函数执行完后,作用域就会被清理,内存也会随之被回收,但是由于闭包函数是建立在函数内部的子函数, 由于其可访问上级作用域,即使上级函数执行完, 作用域也不会随之销毁, 这时的子函数(也就是闭包),便拥有了访问上级作用域中变量的权限,即使上级函数执行完后作用域内的值也不会被销毁。

-   **闭包的特性**：

    -   1、内部函数可以访问定义他们外部函数的参数和变量。(作用域链的向上查找，把外围的作用域中的变量值存储在内存中而不是在函数调用完毕后销毁)设计私有的方法和变量，避免全局变量的污染。

        1.1.闭包是密闭的容器，，类似于set、map容器，存储数据的

        1.2.闭包是一个对象，存放数据的格式为 key-value 形式

    -   2、函数嵌套函数

    -   3、本质是将函数内部和外部连接起来。优点是可以读取函数内部的变量，让这些变量的值始终保存在内存中，不会在函数被调用之后自动清除

-   **闭包形成的条件**：

    1.  函数的嵌套
    1.  内部函数引用外部函数的局部变量，延长外部函数的变量生命周期

-   **闭包的用途**：

    1.  模仿块级作用域
    1.  保护外部函数的变量 能够访问函数定义时所在的词法作用域(阻止其被回收)
    1.  封装私有化变量
    1.  创建模块

-   **闭包应用场景**

    闭包的两个场景，闭包的两大作用：`保存/保护`。 在开发中, 其实我们随处可见闭包的身影, 大部分前端JavaScript 代码都是“事件驱动”的,即一个事件绑定的回调方法; 发送ajax请求成功|失败的回调;setTimeout的延时回调;或者一个函数内部返回另一个匿名函数,这些都是闭包的应用。

-   **闭包的优点**：延长局部变量的生命周期

-   **闭包缺点**：会导致函数的变量一直保存在内存中，过多的闭包可能会导致内存泄漏


# 11、输入url到页面显示过程

> 这个流程可以分为两部分来说，第一部分是浏览器请求响应的过程；

-   输入`URL`：用户在地址栏按下回车，先检查输入的是`搜索关键字`还是符合`url`的规则，然后将其组装成完整 `URL`进行访问；

-   检查缓存：然后会先检查本地`强缓存`是否可用，如果可用就直接从缓存中返回资源；

-   `DNS`解析：如果强缓存不可用，就会进行`DNS`解析，通过`递归查询`和`迭代查询`解析`域名`来得到域名对应的`IP地址`；

    -   `DNS`查询的顺序为：浏览器`IP`缓存，操作系统`IP`缓存，`Hosts`文件，`本地DNS服务器`缓存，`DNS`服务器递归+遍历；
    -   主机向本地 DNS 服务器发出的查询就是**递归查询**，本地 DNS 服务器向各级域名服务器（根域名服务器，顶级域名服务器，权威域名服务器）发出的查询就是**迭代查询** https://juejin.cn/post/6990344840181940261#heading-4

-   建立`TCP`连接：得到`IP`地址后，会进行三次握手去建立`TCP`连接；

-   发送`HTTP`请求：建立`TCP`连接后发送 `HTTP` 请求，发送`HTTP`请求时会携带上`cookie`和`缓存`的标识字段；

-   负载均衡：服务端网关收到`HTTP`请求后，`可能`会有一系列的`负载均衡`处理，通过`反向代理`分配给对应集群中的服务器去执行；

-   服务器返回响应：服务器收到请求后，先根据请求头的`缓存标识`来判断`缓存`是否生效，生效就返回`304`状态码；不生效就返回资源和`200`状态码（在返回`200`的响应报文前，还可能会返回`103`的响应报文）；

-   浏览器接收`HTTP`响应：浏览器接受到`HTTP`响应后根据 `connection:keep-alive` 的值来选择通过 `四次挥手`来断开`TCP`连接，或者保留；

-   同时浏览器还会`缓存`响应头里的`缓存标识字段`；

> 到此为止，浏览器请求响应的过程就结束了；第二部分就是浏览器解析并渲染的过程；

-   构建`DOM`树：浏览器`从上到下`解析 `HTML` 文档生成`DOM`节点树；

-   构建`CSSOM`树：浏览器解析遇到`样式`时，会进行`异步下载`，下载完成后构建 `CSSOM`树；

-   值得一提的是，当遇到不带 `async` 和 `defer` 的 `script` 时，会阻止解析`HTML`并进行下载和执行；

    -   并且`CSS`和`DOM`渲染，`JS`和`DOM`解析之间是有`阻塞关系`的；
    -   `script` ：会阻碍 HTML 解析，只有下载好并执行完脚本才会继续解析 HTML。
    -   `async script` ：解析 HTML 过程中进行脚本的异步下载，下载成功立马执行，有可能会阻断 HTML 的解析。

    -   `defer script`：完全不会阻碍 HTML 的解析，解析完成之后再按照顺序执行脚本。

-   构建渲染树：根据`DOM`节点树和`CSSOM`树构建渲染树（`Render`）；

-   布局（`Layout`）：根据渲染树将`DOM`节点树每一个节点布局在屏幕上的正确位置；

-   绘制（`Paint`）：绘制所有节点，为每一个节点适用对应的样式，绘制到屏幕上；

    -   绘制的过程中还有很多细节，包括说：

    -   构建图层树：需要对`布局树`进行分层，生成`图层树`（比如说Z轴排序）

    -   生成绘制列表：将`图层`的绘制拆分为很多的`绘制指令`，并按`顺序`组成`绘制列表`，并提交到`合成线程`中；

    -   光栅化（`栅格化`）生成位图：`合成线程`将`图层`划分成`图块`，并在`光栅化线程池`中将`图块`转换成`位图`。

        -   同时因为用户只能看到`视口`的这一部分，所以`合成线程`就会按照`视口`附近的`图块`来优先生成`位图`，

    -   显示：一旦所有的图块都被光栅化，合成线程就会提交绘图指令给浏览器进程；浏览器进程生成页面并显示到屏幕上；


# 11、协商缓存/强制缓存

第一次请求：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fcba598a89094a5b9a595e5fd5a53e08~tplv-k3u1fbpfcp-zoom-1.image)

第二次请求相同网页：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/635124116ba34408b343130fbcb299ab~tplv-k3u1fbpfcp-zoom-1.image)

##### 强制缓存
浏览器直接从本地缓存中读取文件，不向服务器发送请求

    - from memory cache 从内存读取缓存
    - from disk cache 从硬盘读取缓存

**header**

- Expires http1.0的头字段,指定过期的具体的一个时间（如 Fri, 02 Sep 2022 08:03:35 GMT），当服务器时间和浏览器时间不一致的话，就会出现问题
- Cache-Control http1.1的头字段，它的值规定了缓存的范围，有以下几个常用的设置值

        （1）max-age：用来设置资源（representations）可以被缓存多长时间，单位为秒；
        （2）s-maxage：和max-age是一样的，不过它只针对代理服务器缓存而言；
        （3）public：指示响应可被任何缓存区缓存；
        （4）private：只能针对个人用户，而不能被代理服务器缓存；
        （5）no-cache：强制客户端直接向服务器发送请求,也就是说每次请求都必须询问服务器缓存是否有效。实际上no-cache是会被缓存的，只不过每次在向客户端（浏览器）提供响应数据时，缓存都要向服务器评估缓存响应的有效性。
        （6）no-store：禁止一切缓存。

##### 协商缓存
向服务器发送请求，服务器根据响应参数判断是否命中协商缓存，返回304则代表缓存无变动，可以从浏览器读取缓存

**header**

1. Etag/If-None-Match

    - 资源过期，浏览器发现上次请求该资源响应头里有Etag,则请求头携带If-None-Match（值是Etag的值）向服务器发出请求，服务求收到请求后比对资源的Etag和请求头的If-None-Match值是否一致，再决定返回200或304
    
    ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0e3ce39d78ae4183869d29fc2ba7f2e6~tplv-k3u1fbpfcp-zoom-1.image)

2. Last-Modifed/If-Modified-Since

    - 资源过期(Cache-Control标识的max-age)，发现上次请求该资源响应头里有Last-Modified，则请求头携带If-Modified-Since（值为Last-Modifed）向服务器发出请求，服务器收到请求后对比修改时间，再决定返回200或304


-   Last-Modifed/If-Modified-Since的时间精度是秒，而Etag可以更精确。
-   Etag优先级是高于Last-Modifed的，所以服务器会优先验证Etag
-   Last-Modifed/If-Modified-Since是http1.0的头字段





# 11、electron

1. 骨架屏

2. 惰性加载

    -   **代码分割 + 预加载**： 代码分割是最常见优化方式。我们把隐藏的内容、或者次优先级的模块拆分出去，启动模块中只保留关键路径。我们也可以在浏览器空闲时预加载这些模块。
    -   **延后加载 Node 模块**： Nodejs 模块的加载和执行需要花费较大的代价, 例如模块查找、模块文件读取、接着才是模块解析和执行。这些操作都是同步了，别忘了，node_modules 黑洞，某块模块可能会引用大量的依赖....  
        Node 应用和 Electron 应用不太一样，通常 Node 服务器应用都会将模块放置在文件顶部, 然后同步加载进来。这个放到 Electron 用户界面上就无法忍受了。 用户界面的启动速度和交互阻塞, 用户是可以感知到的，而且忍耐程度会较低。  
        所以要充分评估模块的大小和依赖。或者可以选择使用打包工具优化和合并 Node 模块。
    -   **划分加载优先级**：既然我们没办法一开始将所有东西都加载出来，那就按照优先级渐进式地将在它们。举个例子，当我们使用 VSCode 打开一个文件时，VScode 会先展示代码面板、接着是目录树、侧边栏、代码高亮、问题面板、初始化各种插件...

3. 打包优化

    -   **减少代码体积**: 现代打包工具有非常多优化手段，例如 Webpack 支持作用域提升、摇树，还有代码压缩、预执行... 这可以合并代码、压缩代码体积，裁剪多余的代码, 减少运行时负担。
    -   **优化I/O**: 我们将模块合并之后，可以减少模块查找和加载的I/O往返。

4. 窗口预热 与 窗口池、窗口常驻

5. 优化进程通信

   - **不要滥用 remote**
   - **异步RPC**
    
https://zhuanlan.zhihu.com/p/420238372


-   高可用

1、资源本地化

2、监控、日志

3、容错

4、对接下游高可用（ssoToken、JSBridge）

-   高性能

1、延后加载 Node 模块

2、Node 使用异步和非阻塞式的版本

3、拆分代码，先加载需要的代码文件，延时启动不必要的进程

4、preload 加载提速

5、清除不必要的网络请求，做缓存、时间间隔，用户可视区域更新

6、打包主进程代码

-   高安全

1、是否考虑启用 启用上下文隔离？

2、Node.js 在渲染层集成是否开启？


# 11、v8垃圾回收

https://juejin.cn/post/6981588276356317214#heading-6

# 11、webpack

### webpack性能优化

1.图片压缩

    - url-loader 中设置 limit 大小来对图片处理，对小于 limit 的图片转化为 base64 格式，其余的不做操作
    - `image-webpack-loader`压缩图片  
    
    {
      test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
      use:[
        {
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: utils.assetsPath('img/[name].[hash:7].[ext]')
          }
        },
        {
          loader: 'image-webpack-loader',
          options: {
            bypassOnDebug: true,
          }
        }
      ]
    }
    

2.减少 ES6 转为 ES5 的冗余代码

    - Babel 插件会在将 ES6 代码转换成 ES5 代码时会注入一些辅助函数，如果多个源代码文件都依赖这些辅助函数，那么这些辅助函数的代码将会出现很多次，造成代码冗余
    - babel-plugin-transform-runtime
    
    // .babelrc
    "plugins": [
        "transform-runtime"
    ]
    

3.优化sourceMap

    - 开发环境推荐： cheap-module-eval-source-map
    - 生产环境推荐： cheap-module-source-map
    
        - cheap： 源代码中的列信息是没有任何作用，因此我们打包后的文件不希望包含列相关信息，只有行信息能建立打包前后的依赖关系。因此不管是开发环境或生产环境，我们都希望添加 cheap 的基本类型来忽略打包前后的列信息；

        -  module ：不管是开发环境还是正式环境，我们都希望能定位到bug的源代码具体的位置，比如说某个 Vue 文件报错了，我们希望能定位到具体的 Vue 文件，因此我们也需要 module 配置；

        -  soure-map ：source-map 会为每一个打包后的模块生成独立的 soucemap 文件 ，因此我们需要增加source-map 属性；

        -  eval-source-map：eval 打包代码的速度非常快，因为它不生成 map 文件，但是可以对 eval 组合使用 eval-source-map 使用会将 map 文件以 DataURL 的形式存在打包后的 js 文件中。在正式环境中不要使用 eval-source-map, 因为它会增加文件的大小，但是在开发环境中，可以试用下，因为他们打包的速度很快。

4. thread-loader开启多进程/多实例构建 


5.压缩代码

        - mini-css-extract-plugin 提取css到单独文件
        - terser-webpack-plugin js压缩
        - optimize-css-assets-webpack-plugin css压缩

6.缩小打包作用域

        - exclude/include (确定 loader 规则范围)
        - resolve.modules 指明第三方模块的绝对路径 (减少不必要的查找)
        - resolve.mainFields 只采用 main 字段作为入口文件描述字段 (减少搜索步骤，需要考虑到所有运行时依赖的第三方模块的入口文件描述字段)
        - resolve.extensions 尽可能减少后缀尝试的可能性
        - noParse 对完全不需要解析的库进行忽略 (不去解析但仍会打包到 bundle 中，注意被忽略掉的文件里不应该包含 import、require、define 等模块化语句)
        - IgnorePlugin (完全排除模块)
        - 合理使用alias
    
7.充分利用缓存提升二次构建速度，babel-loader设置cacheDirectory   

8.代码分割，提取页面公共资源

    - 相同的资源被重复加载，浪费用户的流量和服务器的成本。
    - 每个页面需要加载的资源太大，导致网页首屏加载缓慢，影响用户体验

    - 三种常用的代码分割的方法
        -   Entry Points：入口文件设置的时候可以配置（不同入口打包为不同的bundles,容易引入重复资源）
        -   SplitChunksPlugin（webpack4内置）：进行(公共脚本、基础包、页面公共文件)分离（optimization.splitChunks配置公共资源提取方案）
        -   Dynamic Imports：基于import语法的动态导入，import() 会返回一个Promise。这个模块就会被当作分割点。意味着这个模块和它的子模块都会被分割成一个单独的 chunk。并且，在 webpack 配置文件的 output 字段，需要添加一个 chunkFileName 属性。它决定非入口 chunk 的名称

9.Tree shaking

    - （按需加载）打包过程中检测工程中没有引用过的模块并进行标记，在资源压缩时将它们从最终的bundle中去掉(只能对ES6 Modlue生效) 开发中尽可能使用ES6 Module的模块，提高tree shaking效率
    - 禁用 babel-loader 的模块依赖解析，否则 Webpack 接收到的就都是转换过的 CommonJS 形式的模块，无法进行 tree-shaking
    - purgecss-webpack-plugin去除无用 CSS 代码

### webpack配置中的loader及其作用
Loader就是将Webpack不认识的内容转化为认识的内容,因为webpack 默认支持处理JS与JSON文件，其他类型都处理不了，这里必须借助Loader来对不同类型的文件的进行处理,如常见:
  1. css-loader 处理css
  2. style-loader 以style标签的形式将css样式加载到页面上
  3. postcss-loader 自动添加css3部分属性的浏览器前缀
  4. sass-loader/less-loader 处理less和sass
  5. file-loader 解决图片引入问题，将图片拷贝到指定目录，默认为dist
  6. url-loader 与file-loader类似，图片小于limit值时，将图片转为base64编码
  7. img-loader 压缩图片
  8. babel-loader 使用babel加载es2015+代码并转化为es5

> webpack5 新增资源模块(asset module)，允许使用资源文件（字体，图标等）而无需配置额外的 loader
> 1. asset/resource 将资源分割为单独的文件，并导出 url，类似之前的 file-loader 的功能.
> 2. asset/inline 将资源导出为 dataUrl 的形式，类似之前的 url-loader 的小于 limit 参数时功能.
> 3. asset/source 将资源导出为源码（source code）. 类似的 raw-loader 功能.
> 4. asset 会根据文件大小来选择使用哪种类型，当文件小于 8 KB（默认） 的时候会使用 asset/inline，否则会使用 asset/resource

### webpack配置中的plugin及其作用
与Loader用于转换特定类型的文件不同，插件（Plugin）可以贯穿Webpack打包的生命周期，执行不同的任务
  1. html-webpack-plugin 用于将css或js文件自动引入到html中
  2. clean-webpack-plugin 每次打包的时候，打包目录都会遗留上次打包的文件，为了保持打包目录的纯净，我们需要在打包前将打包目录清空
  3. mini-css-extract-plugin 以css文件的形式将css样式加载到页面上(MiniCssExtractPlugin.loader)
  4. optimize-css-assets-webpack-plugin 压缩css
  5. purgecss-webpack-plugin 单独提取 CSS 并清除用不到的 CSS

### webpack配置中的plugin和loader的区别

`Loader` 本质就是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。 因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。

`Plugin` 就是插件，基于事件流框架 `Tapable`，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

`Loader` 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。

`Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

### 如何编写 Loader ? 介绍一下思路？
### 如何编写 Plugin ? 介绍一下思路？
### Webpack optimize 有配置过吗？可以简单说说吗？
### Webpack 构建流程是怎样的？

Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

-   `初始化参数`：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数
-   `开始编译`：用上一步得到的参数初始化 Compiler 对象，加载所有配置的plugin，执行对象的 run 方法开始执行编译
-   `确定入口`：根据配置中的 entry 找出所有的入口文件
-   `编译模块`：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
-   `完成模块编译`：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
-   `输出资源`：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
-   `输出完成`：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

在以上过程中，`Webpack` 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

简单说

-   初始化：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler
-   编译：从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理
-   输出：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中
  
### tree-shaking 实现原理是怎样的？
https://juejin.cn/post/7135217402983235592#heading-2

**是什么？** `tree-shaking`就是让没有用到的 JS 代码不打包，以减小包的体积 webpack5

**怎么做？**

-   **（1）怎么删**
-   -   a. 使用 ES Modules 语法（即 ES6 的 import 和 export 关键字）
    -   b. CommonJS 语法无法 tree-shaking（即 require 和 exports 语法），故不要用
    -   -   i. 有时候 babel-loader 会自动把代码编程 CommonJS 代码，需要给 babel-loader 添加`modules:false`选项
    -   c. 引入的时候只引用需要的模块
    -   -   i. 要写 import { cloneDeep } from 'lodash-es' 因为方便 tree-shaking
        -   ii. 不要写 import _ from 'lodash'，因为会导致无法 tree-shaking 无用模块
-   **（2）怎么不删：在 package.json 中配置 sideEffects，防止某些文件被删掉**
    
    -   sideEffect的定义是，在导入时会执行特殊行为的代码
    -   a. 比如 import 了 x.js，而 x.js 只是添加了 window.x 属性，那么 x.js 就要放到 sideEffects 里
    -   b. 比如所有被 import 的 CSS 都要放在 sideEffects 里
-   **（3）怎么开启：在 webpack config 中将 mode设置为 production（开发环境没必要 tree-shaking）**
    -   webpack5当打包环境为`mode:production`时，默认开启`tree-shaking`功能


### Webpack 热更新（HMR）是如何实现？
`webpack-dev-server`提供了一个基本的 web server，并且具有实时重新加载功能
```
module.export = {
    devServer: {
        contentBase: './dist',
        hot: true, // 热更新
      },
}
```
`Webpack` 的热更新又称热替换（`Hot Module Replacement`），缩写为 `HMR`。 这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

HMR的核心就是客户端从服务端拉去更新后的文件，准确的说是 chunk diff (chunk 需要更新的部分)，实际上 WDS 与浏览器之间维护了一个 `Websocket`，当本地资源发生变化时，WDS（webpack-dev-server） 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比出差异后会向 WDS 发起 `Ajax` 请求来获取更改内容(文件列表、hash)，这样客户端就可以再借助这些信息继续向 WDS 发起 `jsonp` 请求获取该chunk的增量更新。

后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 `HotModulePlugin` 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像`react-hot-loader` 和 `vue-loader` 都是借助这些 API 实现 HMR。

### Webpack 打包中 Babel 插件是如何工作的？
Babel大概分为三大部分：  

    - 解析：将代码转换成 AST  
        - 词法分析：将代码(字符串)分割为token流，即语法单元成的数组
        - 语法分析：分析token流(上面生成的数组)并生成 AST 
    - 转换：访问 AST 的节点进行变换操作生产新的 AST
    - 生成：以新的 AST 为基础生成代码
    
### Webpack 和 Rollup 有什么相同点与不同点？
### Webpack5 更新了哪些新特性？

### prefetch和preload
  + prefetch(预获取) : 浏览器空闲的时候进行资源的拉取
  ```
  // 但是如果需要异步加载的文件比较大时，在点击的时候去加载也会影响到我们的体验，这个时候我们就可以考虑使用 prefetch 来进行预拉取
  img.addEventListener('click', () => {
    import( /* webpackPrefetch: true */ './desc').then(({ default: element }) => {
      console.log(element)
      document.body.appendChild(element)
    })
  })
  ```
  + preload（预加载） : 提前加载后面会用到的关键资源，因为会提前拉取资源，如果不是特殊需要，谨慎使用

### Webpack 内置 hash

-   **hash**: 项目每次构建都会生成一个hash，和整个项目有关，项目任意地方有改变就会改变

    hash会更据每次工程的内容进行计算，很容易造成不必要的hash变更，不利于版本管理。 一般来说，没有什么机会直接使用hash。

<!---->

-   **content hash**: 和单个文件的内容相关。指定文件的内容发生改变，就会改变hash，内容不变hash 值不变

    对于css文件来说，一般会使用MiniCssExtractPlugin将其抽取为一个单独的css文件。

    此时可以使用contenthash进行标记，确保css文件内容变化时，可以更新hash。

-   **chunk hash**：和webpack打包生成的chunk相关。每一个entry，都会有不同的hash。

    一般来说，针对于输出文件，我们使用chunkhash。

    因为webpack打包后，最终每个entry文件及其依赖会生成单独的一个js文件。

    此时使用chunkhash，能够保证整个打包内容的更新准确性。

### Webpack vs Vite
1. 两者的打包过程以及原理
> Webpack
>
> -   Webpack 叫做 **bundler** ，将所有文件打包成一个文件。
> -   Webpack 先识别入口文件，启动服务器后，最后直接给出打包结果。Webpack 做的是分析代码，转换代码，最后形成打包后的代码。

> Vite
>
> -   Vite 又叫做 **no bundler** ，借助了浏览器对ESM规范的支持，不用打包，支持 ES moudle 加载。
> -   Vite 启动服务器后，会按需加载，当请求哪个模块时才会对该模块进行编译。按需加载的方式，极大的缩减了编译时间。
   
    1.优点：快速的冷启动、快速的热更新
    2.缺点：较差的首屏性能、较差的懒加载性能
        由于 `unbundle` 机制，首屏期间需要额外做以下工作:
        -   不对源文件做合并捆绑操作，导致大量的 `http` 请求；
        -   `dev server` 运行期间对源文件做 `resolve`、`load`、`transform`、`parse` 操作；
        -   预构建、二次预构建操作也会阻塞首屏请求，直到预构建完成为止；

### **（1）开发环境区别**

-   a.`  vite ` 自己实现 server，不对代码打包，充分利用浏览器对`<script type=module>`的支持，即`<script type=module src="main.js">`
-   -   i. 假设 main.js 引入了 vue
    -   ii. 该 server 会把 `import {createApp} from 'vue' `改为`import {createApp} from "/node_modules/.vite/vue.js" `这样浏览器就知道去哪里找 vue.js 了
-   b. `webpack-dev-server` 常使用 `babel-loader` 基于内存打包，比 vite 慢很多很多很多
-   -   i. 比如请求 main.js，main.js 如果引用了 vue.js，webpack-dev-server 会把 vue.js 代码拷贝到main.js，即该 server 会把 vue.js 的代码（递归地）打包进 main.js

### **（2）生产环境区别**

-   a. vite 使用 rollup + esbuild（用 GO 语言写的） 来打包 JS 代码
-   b. webpack 使用 babel （用JS写的）来打包 JS 代码，比 esbuild 慢很多很多很多
-   -   i. webpack 能使用 esbuild 吗？可以，但需要自己配置（很麻烦）

### **（3）文件处理时机**

-   a. vite 只会在你请求某个文件的时候处理该文件
-   b. webpack 会提前打包好 main.js，等你请求的时候直接输出打包好的 JS 给你

目前已知 vite 的**缺点**有：

-   热更新常常失败，原因不清楚，页面刷新可解决
-   有些功能 rollup 不支持，需要自己写 rollup 插件
-   不支持非现代浏览器

链接：https://juejin.cn/post/6844904094281236487  




# 11、cdn
CDN (全称 Content Delivery Network)，即内容分发网络

构建在现有网络基础之上的智能虚拟网络，依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。`CDN` 的关键技术主要有内容存储和分发技术

简单来讲，`CDN`就是根据用户位置分配最近的资源

于是，用户在上网的时候不用直接访问源站，而是访问离他“最近的”一个 CDN 节点，术语叫**边缘节点**，其实就是缓存了源站内容的代理服务器

## 原理分析

 在没有应用`CDN`时，我们使用域名访问某一个站点时的路径为

> 用户提交域名→浏览器对域名进行解释→`DNS` 解析得到目的主机的IP地址→根据IP地址访问发出请求→得到请求数据并回复

应用`CDN`后，`DNS` 返回的不再是 `IP` 地址，而是一个`CNAME`(Canonical Name ) （权威域名服务器返回）别名记录，指向`CDN`的全局负载均衡

#### 1. 负载均衡系统

由于没有返回`IP`地址，于是本地`DNS`会向负载均衡系统再发送请求 ，则进入到`CDN`的全局负载均衡系统进行智能调度：

-   看用户的 IP 地址，查表得知地理位置，找相对最近的边缘节点
-   看用户所在的运营商网络，找相同网络的边缘节点
-   检查边缘节点的负载情况，找负载较轻的节点
-   其他，比如节点的“健康状况”、服务能力、带宽、响应时间等

结合上面的因素，得到最合适的边缘节点，然后把这个节点返回给用户，用户就能够就近访问`CDN`的缓存代理

整体流程如下图：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/08382c3d37ed4097815d11d8831e5f72~tplv-k3u1fbpfcp-zoom-1.image)

#### 2. 缓存代理

缓存系统是 ` CDN  `的另一个关键组成部分，缓存系统会有选择地缓存那些最常用的那些资源

其中有两个衡量`CDN`服务质量的指标：

-   命中率：用户访问的资源恰好在缓存系统里，可以直接返回给用户，命中次数与所有访问次数之比
-   回源率：缓存里没有，必须用代理的方式回源站取，回源次数与所有访问次数之比

缓存系统也可以划分出层次，分成一级缓存节点和二级缓存节点。一级缓存配置高一些，直连源站，二级缓存配置低一些，直连用户

回源的时候二级缓存只找一级缓存，一级缓存没有才回源站，可以有效地减少真正的回源

现在的商业 `CDN`命中率都在 90% 以上，相当于把源站的服务能力放大了 10 倍以上



# 11、typeof instance of
### typeof 实现原理

`typeof` 一般被用于判断一个变量的类型，我们可以利用 `typeof` 来判断`number`, `string`, `object`, `boolean`, `function`, `undefined`, `symbol` 这七种类型，这种判断能帮助我们搞定一些问题，比如在判断不是 object 类型的数据的时候，`typeof`能比较清楚的告诉我们具体是哪一类的类型。但是，很遗憾的一点是，`typeof` 在判断一个 object的数据的时候只能告诉我们这个数据是 object, 而不能细致的具体到是哪一种 object

其实，js 在底层存储变量的时候，会在变量的机器码的低位1-3位存储其类型信息👉

-   000：对象
-   010：浮点数
-   100：字符串
-   110：布尔
-   1：整数

but, 对于 `undefined` 和 `null` 来说，这两个值的信息存储是有点特殊的。

`null`：所有机器码均为0

`undefined`：用 −2^30 整数来表示

所以，`typeof` 在判断 `null` 的时候就出现问题了，由于 `null` 的所有机器码均为0，因此直接被当做了对象来看待


### instanceof 实现原理

`instanceof` 主要的实现原理就是只要右边变量的 `prototype` 在左边变量的原型链上即可。因此，`instanceof` 在查找的过程中会遍历左边变量的原型链，直到找到右边变量的 `prototype`，如果查找失败，则会返回 false，告诉我们左边变量并非是右边变量的实例
  
```
function new_instance_of(leftVaule, rightVaule) { 
    let rightProto = rightVaule.prototype; // 取右表达式的 prototype 值
    leftVaule = leftVaule.__proto__; // 取左表达式的__proto__值
    while (true) {
    	if (leftVaule === null) {
            return false;	
        }
        if (leftVaule === rightProto) {
            return true;	
        } 
        leftVaule = leftVaule.__proto__ 
    }
}
```

### Object.prototype.toString 原理
调用object原型方法toString判断
在toString方法被调用时,会执行以下几个操作步骤～

1.  获取`this指向`的那个对象的`[[Class]]`属性的值。 *（这也是我们为什么要用call改变this指向的原因）*
1.  计算出三个字符串"[object "、 第一步的操作结果Result(1)、 以及 "]" 连接后的新字符串。
1.  返回第二步的操作结果Result(2)，也就是类似 `[object className]` 这种格式字符串。



作者：nicole_zhang18970  
链接：https://juejin.cn/post/6844903613584654344  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

# 11、js数据类型

## 基本数据类型

Number、String、Boolean、Null、Undefind、BigInt、Symbol

#### Symbol：

```
    let a = Symbol() // symBol创建
```
##### symbol的应用

`1.作为对象的属性`

如果我们在不了解一个对象的时候，想为其添加一个方法或者属性，又怕键名重复引起覆盖的问题，而这个时候我们就需要一个**唯一**性的键来解决这个问题，于是`Symbol`出场了，它可以作为对象的属性的键，并键名避免冲突

```
    let a = Symbol() 
    let obj = {} 
    obj[a] = "hello world"
```

`2.降低代码耦合`

**与代码强耦合的字符串**，可以理解为：与我们的程序代码强制绑定在一起，然而这会导致一个问题，在条件判断复杂的情况下，我们想要更改我们的判断条件，就需要更改每一个判断控制，维护起来非常麻烦，所以我们可以换一种形式来解决字符串与代码强耦合

```
switch (name) {
        case "猪痞恶霸"
        console.log(1)
        case "Ned"
        console.log(2)
}

// 优化后
const judge = {
    rectangle:Symbol("rectangle"),
    triangle:Symbol("triangle")
}
function getArea(model, size) {
    switch (model) {
        case judge.rectangle:
            return size.width * size.height
        case judge.triangle:
            return size.width * size.height / 2
    }
}
let area = getArea(judge.rectangle ,{width:100, height:200})
console.log(area)

```

`3.全局共享Symbol`

如果我们想在不同的地方调用已经同一`Symbol`即全局共享的`Symbol`，可以通过`Symbol.for()`方法，参数为创建时传入的描述字符串，该方法可以遍历**全局注册表**中的的`Symbol`，当搜索到相同描述，那么会调用这个`Symbol`，如果没有搜索到，就会创建一个新的`Symbol`

`4. 属性私有`

假如你有一个对象，里头有一个不想暴露给外界的属性，难不成你起个很吓唬人的名字不让用户用？人家该用还是继续用。用Symbol就能阻止内部属性被访问

#### BigInt：

JS 中的`Number`类型只能安全地表示`-9007199254740991 (-(2^53-1))` 和`9007199254740991(2^53-1)`之间的整数，任何超出此范围的整数值都可能失去精度

`BigInt`是一种新的数据类型，用于当整数值大于`Number`数据类型支持的范围时。这种数据类型允许我们安全地对大整数执行算术操作，表示高分辨率的时间戳，使用大整数id，等等，而不需要使用库

重要的是要记住，不能使用`Number`和`BigInt`操作数的混合执行算术运算，需要通过显式转换其中的一种类型。 此外，出于兼容性原因，不允许在`BigInt`上使用一元加号（`+`）运算符。

## 引用数据类型

Object、Array、Function

# 11、websocket

https://juejin.cn/post/7086021621542027271#heading-9

WebSocket 是基于 TCP 的一种新的应用层网络协议。它实现了浏览器与服务器全双工通信，即允许服务器主动发送信息给客户端。因此，在 WebSocket 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输，客户端和服务器之间的数据交换变得更加简单。
 
#### 2.1、WebSocket 的特点

-   建立在 TCP 协议之上；
-   与 HTTP 协议有着良好的兼容性：默认端口也是 80（ws） 和 443(wss，运行在 TLS 之上)，并且握手阶段采用 HTTP 协议；
-   较少的控制开销：连接创建后，ws 客户端、服务端进行数据交换时，协议控制的数据包头部较小，而 HTTP 协议每次通信都需要携带完整的头部；
-   可以发送文本，也可以发送二进制数据；
-   没有同源限制，客户端可以与任意服务器通信；
-   协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL；
-   支持扩展：ws 协议定义了扩展，用户可以扩展协议，或者实现自定义的子协议（比如支持自定义压缩算法等）；

#### 2.2、 WebSocket 与 HTTP、TCP

HTTP、WebSocket 等协议都是处于 OSI 模型的最高层：应用层。而 IP 协议工作在网络层，TCP 协议工作在传输层。

HTTP、WebSocket 等应用层协议，都是基于 TCP 协议来传输数据的，因此其连接和断开，都要遵循 TCP 协议中的三次握手和四次挥手 ，只是在连接之后发送的内容不同，或者是断开的时间不同。

#### 2.3、 HTML5 与 WebSocket

WebSocket API 是 HTML5 标准的一部分， 但这并不代表 WebSocket 一定要用在 HTML 中，或者只能在基于浏览器的应用程序中使用。 实际上，许多语言、框架和服务器都提供了 WebSocket 支持，例如：

-   基于 C 的 libwebsocket.org
-   基于 Node.js 的 Socket.io
-   基于 Python 的 ws4py
-   基于 C++ 的 WebSocket++
-   Apache 对 WebSocket 的支持：Apache Module mod_proxy_wstunnel
-   Nginx 对 WebSockets 的支持：NGINX as a WebSockets Proxy 、 NGINX Announces Support for WebSocket Protocol 、WebSocket proxying


虽然HTTP/2也具备服务器推送功能，但HTTP/2 只能推送静态资源，无法推送指定的信息

# 11、css-loader/style-loader


 css-loader是webpack中的一个loader，用于解析css文件并将其转换为js模块。
 
 它的原理是通过使用正则表达式匹配css文件中的import、url等语句，将其替换为require语句，以便在js中引用css文件。
 
 css-loader还支持模块化，可以将css文件中的类名等选择器转换为局部作用域，避免全局污染

-   css-loader帮助我们解析css成为js对象
-   sytle-loader可以从css-loader解析的对象中提取css样式挂载到页面当中

# 11、vue-loader

    -   `vue-loader`是用于处理单文件组件（SFC，Single-File Component）的webpack loader

    -   因为有了`vue-loader`，我们就可以在项目中编写SFC格式的Vue组件，我们可以把代码分割为<template>、<script>和<style>，代码会异常清晰。结合其他loader我们还可以用Pug编写<template>，用SASS编写<style>，用TS编写<script>。我们的<style>还可以单独作用当前组件。

    -   webpack打包时，会以loader的方式调用`vue-loader`

    -   `vue-loader`被执行时，它会对SFC中的每个语言块用单独的loader链处理。最后将这些单独的块装配成最终的组件模块。

 ### 知其所以然

0.  `vue-loader`会调用`@vue/compiler-sfc`模块解析SFC源码为一个描述符（Descriptor），然后为每个语言块生成import代码，返回的代码类似下面：

```
// source.vue被vue-loader处理之后返回的代码
​
// import the <template> block
import render from 'source.vue?vue&type=template'
// import the <script> block
import script from 'source.vue?vue&type=script'
export * from 'source.vue?vue&type=script'
// import <style> blocks
import 'source.vue?vue&type=style&index=1'
​
script.render = render
export default script
复制代码
```

* * *

2.  我们想要script块中的内容被作为js处理（当然如果是`<script lang="ts">`被作为ts处理），这样我们想要webpack把配置中跟.js匹配的规则都应用到形如`source.vue?vue&type=script`的这个请求上。例如我们对所有*.js配置了babel-loader，这个规则将被克隆并应用到所在Vue SFC的

```
import script from 'source.vue?vue&type=script'
复制代码
```

将被展开为：

```
import script from 'babel-loader!vue-loader!source.vue?vue&type=script'
复制代码
```

类似的，如果我们对`.sass`文件配置了`style-loader` + `css-loader` + `sass-loader`，对下面的代码：

```
<style scoped lang="scss">
复制代码
```

`vue-loader`将会返回给我们下面结果：

```
import 'source.vue?vue&type=style&index=1&scoped&lang=scss'
复制代码
```

* * *

然后webpack会展开如下：

```
import 'style-loader!css-loader!sass-loader!vue-loader!source.vue?vue&type=style&index=1&scoped&lang=scss'
复制代码
```

0.  当处理展开请求时，`vue-loader`将被再次调用。这次，loader将会关注那些有查询串的请求，且仅针对特定块，它会选中特定块内部的内容并传递给后面匹配的loader。
0.  对于`<script>`块，处理到这就可以了，但是`<template>` 和 `<style>`还有一些额外任务要做，比如：

-   需要用Vue 模板编译器编译template，从而得到render函数
-   需要对`<style scoped>`中的CSS做后处理（post-process），该操作在css-loader之后但在style-loader之前

实现上这些附加的loader需要被注入到已经展开的loader链上，最终的请求会像下面这样：

```
// <template lang="pug">
import 'vue-loader/template-loader!pug-loader!source.vue?vue&type=template'
​
// <style scoped lang="scss">
```
-  vue-loader是webpack中的一个loader，用于解析.vue文件并将其转换为js模块。
-  它的原理是通过使用正则表达式匹配.vue文件中的template、script、style等标签，将其分离出来，然后分别进行处理。
-  对于template标签，vue-loader会将其转换为render函数，以便在js中渲染组件。
-  对于script标签，vue-loader会将其转换为CommonJS模块，以便在js中引用组件。
-  对于style标签，vue-loader会将其转换为js模块，以便在js中引用样式。
-  vue-loader还支持模块化，可以将.vue文件中的样式和模板转换为局部作用域，避免全局污染。

# 11、函数柯里化

**Currying** 为实现多参函数提供了一个递归降解的实现思路——**把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函**


**核心思想**：比较多次接受的参数总数与函数定义时的入参数量，当接受参数的数量大于或等于被 Currying 函数的传入参数数量时，就返回计算结果，否则返回一个继续接受参数的函数。
```
function trueCurrying(fn, ...args) {

    if (args.length >= fn.length) {

        return fn(...args)

    }

    return function (...args2) {

        return trueCurrying(fn, ...args, ...args2)

    }
}
```

## Currying 使用场景

### 参数复用

固定不变的参数，实现参数复用是 **Currying** 的主要用途之一。

上文中的`increment`, `addTen`是一个参数复用的实例。对`add`方法固定第一个参数为 10 后，改方法就变成了一个将接受的变量值加 10 的方法。

### 延迟执行

延迟执行也是 **Currying** 的一个重要使用场景，同样 bind 和箭头函数也能实现同样的功能。

  


# 11、vue性能优化

1. 路由懒加载
2. keep-alive缓存
3. v-show复用dom
4. v-once只渲染一次，v-memo给一个元素或组件打上一个标识，在下次渲染的时候如和标识一样则跳过更新（包括组件的子节点），即使是虚拟 DOM 节点的 创建也将被跳过
5. 长列表优化，文档碎片，虚拟滚动
 
        **文档碎片**是一种虚拟的DOM节点，存在于内存中，跟实际的DOM节点之间没有关系，当我们需要给一个节点中插入多个子节点的时候，完全可以将多个子节点先插入到文档碎片中，所有子节点都放到文档碎片中后，再将文档碎片插入到实际的节点中，这样就减少了很多节点直接插入到父节点中的次数了，也就是本来多次触发重排重绘的操作，有了文档碎片中，只需要触发一次重排重绘了
        ```
        for(var i=0;i<5;i++)
        { 
            var op = document.createElement("span"); 
            var oText = document.createTextNode(i); 
            op.appendChild(oText); 
            document.body.appendChild(op); 
        }
        ```

6. 图片懒加载、骨架屏
7. 按需引入
8. 图片使用svg
9. 第三方资源cdn
10. 开启gzip
11. 浏览器缓存
12. webpack优化

<!---->

-   最常见的路由懒加载：有效拆分App尺寸，访问时才异步加载

    ```
    const router = createRouter({
      routes: [
        // 借助webpack的import()实现异步组件
        { path: '/foo', component: () => import('./Foo.vue') }
      ]
    })
    1.当打包构建应用时，JavaScript 包会变得非常大，影响页面加载。利用路由懒加载我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样会更加高效，是一种优化手段。
    2.一般来说，对所有的路由**都使用动态导入**是个好主意。
    3.给`component`选项配置一个返回 Promise 组件的函数就可以定义懒加载路由。例如：`{ path: '/users/:id', component: () => import('./views/UserDetails') }`,结合注释`() => import(/* webpackChunkName: "group-user" */ './UserDetails.vue')`可以做webpack代码分块,vite中结合rollupOptions定义分块
    4.  路由中不能使用异步组件
    ```

-   `keep-alive`缓存页面：避免重复创建组件实例，且能保留缓存组件状态

    ```
    <router-view v-slot="{ Component }">
      <keep-alive>
        <component :is="Component"></component>
      </keep-alive>
    </router-view>
    ```

-   使用`v-show`复用DOM：避免重复创建组件

    ```
    <template>
      <div class="cell">
        <!-- 这种情况用v-show复用DOM，比v-if效果好 -->
        <div v-show="value" class="on">
          <Heavy :n="10000"/>
        </div>
        <section v-show="!value" class="off">
          <Heavy :n="10000"/>
        </section>
      </div>
    </template>
    ```

-   `v-for` 遍历避免同时使用 `v-if`：实际上在Vue3中已经是个错误写法

    ```
    <template>
        <ul>
          <li
            v-for="user in activeUsers"
            <!-- 避免同时使用，vue3中会报错 -->
            <!-- v-if="user.isActive" -->
            :key="user.id">
            {{ user.name }}
          </li>
        </ul>
    </template>
    <script>
      export default {
        computed: {
          activeUsers: function () {
            return this.users.filter(user => user.isActive)
          }
        }
      }
    </script>
    ```

-   v-once和v-memo：不再变化的数据使用`v-once`

    ```
    <!-- single element -->
    <span v-once>This will never change: {{msg}}</span>
    <!-- the element have children -->
    <div v-once>
      <h1>comment</h1>
      <p>{{msg}}</p>
    </div>
    <!-- component -->
    <my-component v-once :comment="msg"></my-component>
    <!-- `v-for` directive -->
    <ul>
      <li v-for="i in list" v-once>{{i}}</li>
    </ul>
    ```

    按条件跳过更新时使用`v-memo`：下面这个列表只会更新选中状态变化项

    ```
    <div v-for="item in list" :key="item.id" v-memo="[item.id === selected]">
      <p>ID: {{ item.id }} - selected: {{ item.id === selected }}</p>
      <p>...more child nodes</p>
    </div>
    ```

    > [vuejs.org/api/built-i…](https://link.juejin.cn?target=https%3A%2F%2Fvuejs.org%2Fapi%2Fbuilt-in-directives.html%23v-memo "https://vuejs.org/api/built-in-directives.html#v-memo")

<!---->

-   长列表性能优化：如果是大数据长列表，可采用虚拟滚动，只渲染少部分区域的内容

    ```
    <recycle-scroller
      class="items"
      :items="items"
      :item-size="24"
    >
      <template v-slot="{ item }">
        <FetchItemView
          :item="item"
          @vote="voteItem(item)"
        />
      </template>
    </recycle-scroller>
    ```

    > 一些开源库：
    >
    > -   [vue-virtual-scroller](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FAkryum%2Fvue-virtual-scroller "https://github.com/Akryum/vue-virtual-scroller")
    > -   [vue-virtual-scroll-grid](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Frocwang%2Fvue-virtual-scroll-grid "https://github.com/rocwang/vue-virtual-scroll-grid")

-   事件的销毁：Vue 组件销毁时，会自动解绑它的全部指令及事件监听器，但是仅限于组件本身的事件。

    ```
    export default {
      created() {
        this.timer = setInterval(this.refresh, 2000)
      },
      beforeUnmount() {
        clearInterval(this.timer)
      }
    }
    ```

-   图片懒加载

    对于图片过多的页面，为了加速页面加载速度，所以很多时候我们需要将页面内未出现在可视区域内的图片先不做加载， 等到滚动到可视区域后再去加载。

    ```
    <img v-lazy="/static/img/1.png">
    ```

    > 参考项目：[vue-lazyload](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fhilongjw%2Fvue-lazyload "https://github.com/hilongjw/vue-lazyload")

-   第三方插件按需引入

    像`element-plus`这样的第三方组件库可以按需引入避免体积太大。

    ```
    import { createApp } from 'vue';
    import { Button, Select } from 'element-plus';
    
    const app = createApp()
    app.use(Button)
    app.use(Select)
    ```

-   子组件分割策略：较重的状态组件适合拆分

    ```
    <template>
      <div>
        <ChildComp/>
      </div>
    </template>
    
    <script>
    export default {
      components: {
        ChildComp: {
          methods: {
            heavy () { /* 耗时任务 */ }
          },
          render (h) {
            return h('div', this.heavy())
          }
        }
      }
    }
    </script>
    ```

    但同时也不宜过度拆分组件，尤其是为了所谓组件抽象将一些不需要渲染的组件特意抽出来，组件实例消耗远大于纯dom节点。参考：[vuejs.org/guide/best-…](https://link.juejin.cn?target=https%3A%2F%2Fvuejs.org%2Fguide%2Fbest-practices%2Fperformance.html%23avoid-unnecessary-component-abstractions "https://vuejs.org/guide/best-practices/performance.html#avoid-unnecessary-component-abstractions")

-   服务端渲染/静态网站生成：SSR/SSG

    如果SPA应用有首屏渲染慢的问题，可以考虑SSR、SSG方案优化。参考[SSR Guide](https://link.juejin.cn?target=https%3A%2F%2Fvuejs.org%2Fguide%2Fscaling-up%2Fssr.html "https://vuejs.org/guide/scaling-up/ssr.html")
    
    
### 大量数据优化

-   在大型企业级项目中经常需要渲染大量数据，此时很容易出现卡顿的情况。比如大数据量的表格、树。

-   处理时要根据情况做不通处理：

    -   可以采取分页的方式获取，避免渲染大量数据
    -   [vue-virtual-scroller](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FAkryum%2Fvue-virtual-scroller "https://github.com/Akryum/vue-virtual-scroller")等虚拟滚动方案，只渲染视口范围内的数据
    -   如果不需要更新，可以使用`v-once`方式只渲染一次
    -   通过[v-memo](https://link.juejin.cn?target=https%3A%2F%2Fvuejs.org%2Fapi%2Fbuilt-in-directives.html%23v-memo "https://vuejs.org/api/built-in-directives.html#v-memo")可以缓存结果，结合`v-for`使用，避免数据变化时不必要的VNode创建
    -   可以采用懒加载方式，在用户需要的时候再加载数据，比如tree组件子树的懒加载

-   总之，还是要看具体需求，首先从设计上避免大数据获取和渲染；实在需要这样做可以采用虚表的方式优化渲染；最后优化更新，如果不需要更新可以v-once处理，需要更新可以v-memo进一步优化大数据更新性能。其他可以采用的是交互方式优化，无线滚动、懒加载等方案。



![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/27c4e2af712147ee869291015516a4ad~tplv-k3u1fbpfcp-watermark.image?)


# 11、diff算法

https://juejin.cn/post/6994959998283907102

1.Vue中的diff算法称为patching算法，它由Snabbdom修改而来，虚拟DOM要想转化为真实DOM就需要通过patch方法转换。

2.最初Vue1.x视图中每个依赖均有更新函数对应，可以做到精准更新，因此并不需要虚拟DOM和patching算法支持，但是这样粒度过细导致Vue1.x无法承载较大应用；Vue 2.x中为了降低Watcher粒度，每个组件只有一个Watcher与之对应，此时就需要引入patching算法才能精确找到发生变化的地方并高效更新。

3.vue中diff执行的时刻是组件内响应式数据变更触发实例执行其更新函数时，更新函数会再次执行render函数获得最新的虚拟DOM，然后执行patch函数，并传入新旧两次虚拟DOM，通过比对两者找到变化的地方，最后将其转化为对应的DOM操作。

* * *

4.patch过程是一个递归过程，遵循深度优先、同层比较的策略；以vue3的patch为例：

-   首先判断两个节点是否为相同同类节点，不同则删除重新创建

-   如果双方都是文本则更新文本内容

-   如果双方都是元素节点则递归更新子元素，执行`updateChildren`函数，同时更新元素属性

-   更新子节点时又分了几种情况：

-   新的子节点是文本，老的子节点是数组则清空，并设置文本；

-   新的子节点是文本，老的子节点是文本则直接更新文本；

-   新的子节点是数组，老的子节点是文本则清空文本，并创建新子节点数组中的子元素；

-   新的子节点是数组，老的子节点也是数组，那么比较两组子节点，updateChildren

5.  vue3中引入的更新策略：编译期优化patchFlags、block等

sameNode 
```
function sameVnode(oldVnode, newVnode) {
  return (
    oldVnode.key === newVnode.key && // key值是否一样
    oldVnode.tagName === newVnode.tagName && // 标签名是否一样
    oldVnode.isComment === newVnode.isComment && // 是否都为注释节点
    isDef(oldVnode.data) === isDef(newVnode.data) && // 是否都定义了data
    sameInputType(oldVnode, newVnode) // 当标签为input时，type必须是否相同
  )
}
```
patchVNode 

```

function patchVnode(oldVnode, newVnode) {
  const el = newVnode.el = oldVnode.el // 获取真实DOM对象
  // 获取新旧虚拟节点的子节点数组
  const oldCh = oldVnode.children, newCh = newVnode.children
  // 如果新旧虚拟节点是同一个对象，则终止
  if (oldVnode === newVnode) return
  // 如果新旧虚拟节点是文本节点，且文本不一样
  if (oldVnode.text !== null && newVnode.text !== null && oldVnode.text !== newVnode.text) {
    // 则直接将真实DOM中文本更新为新虚拟节点的文本
    api.setTextContent(el, newVnode.text)
  } else {
    // 否则

    if (oldCh && newCh && oldCh !== newCh) {
      // 新旧虚拟节点都有子节点，且子节点不一样

      // 对比子节点，并更新
      updateChildren(el, oldCh, newCh)
    } else if (newCh) {
      // 新虚拟节点有子节点，旧虚拟节点没有

      // 创建新虚拟节点的子节点，并更新到真实DOM上去
      createEle(newVnode)
    } else if (oldCh) {
      // 旧虚拟节点有子节点，新虚拟节点没有

      //直接删除真实DOM里对应的子节点
      api.removeChild(el)
    }
  }
}
```

updateChildren
```
首尾指针法
-   1、` oldS 和 newS  `使用`sameVnode方法`进行比较，`sameVnode(oldS, newS)`
-   2、` oldS 和 newE  `使用`sameVnode方法`进行比较，`sameVnode(oldS, newE)`
-   3、` oldE 和 newS  `使用`sameVnode方法`进行比较，`sameVnode(oldE, newS)`
-   4、` oldE 和 newE  `使用`sameVnode方法`进行比较，`sameVnode(oldE, newE)`
-   5、如果以上逻辑都匹配不到，再把所有旧子节点的 `key` 做一个映射到旧节点下标的 `key -> index` 表，然后用新 `vnode` 的 `key` 去找出在旧节点中可以复用的位置。

```

### vue3 diff算法优化

https://juejin.cn/post/7090526650059718686

**patchFlag**：属性可以让运行时知道这个vnode是一个含有动态内容的vnode，而且根据这个属性的值还可以精准的判断标签的动态部分到底在哪。
所以可以在创建VNode的时候，把Vnode子节点中的被patchFlag标记的动态vnode提取出来，保存在它自己的dynamicChildren数组内。

**block**：我们把含有这个dynamicChildren数组的VNode就叫做一个block，block不只会提取到children的动态VNode，还可以提取到所有子代动态VNode。为了同时实现上面提到的block嵌套和当前动态vnode收集 ，vue设计一个类似于栈的数据结构来让不同层级的block收集含有当前动态内容的VNode

```
function patchElement(oldVNode, newVNode) {
  //省略部分代码

  //处理节点的props
  //简单模拟patchFlages进行靶向更新，实际上源码中的patchFlags更复杂
   if(newVNode.patchFlags){
    //有patchFlags，靶向更新
    if(patchFlags == 1){
    //只更新文本节点
    }else if(patchFlags == 2){
    //只更新class绑定
    }else(patchFlags == 3){
    //...
    }
   }else{
   //进行全量更新，遍历props
   }

   //处理子节点
   if(newVNode.dynamicChildren){
    //有dynamicChildren，说明oldVNode, newVNode是block，可以进行靶向更新
     patchBlockChildren(oldVNode,newVNode)
   }else{
     //传统diff算法更新
     patchChildren(oldVNode,newVNode)
   }

   //省略部分代码
  }
  
  function patchBlockChildren(oldVNode,newVNode) {
    //收集了所有的子动态vnode，遍历所有它们并更新
    for (let i=0;i<newVNode.dynamicChildren.length;i++){
      patchElement(oldVNode.dynamicChildren[i],newVNode.dynamicChildren[i])
    }
  }
```

事件缓存
静态提升

vue3 diff算法采用贪心+二分
https://leetcode.cn/problems/longest-increasing-subsequence/description/?q=js&orderBy=most_relevant


# 11、this指向

### 箭头函数调用模式

先看箭头函数和普通函数的重要区别：

> 1、没有自己的`this`、`super`、`arguments`和`new.target`绑定。 2、不能使用`new`来调用。 3、没有原型对象。 4、不可以改变`this`的绑定。 5、形参名称不能重复。

箭头函数中没有`this`绑定，必须通过查找作用域链来决定其值。 如果箭头函数被非箭头函数包含，则`this`绑定的是最近一层非箭头函数的`this`，否则`this`的值则被设置为全局对象。 比如：

```
var name = 'window';
var student = {
    name: '若川',
    doSth: function(){
        // var self = this;
        var arrowDoSth = () => {
            // console.log(self.name);
            console.log(this.name);
        }
        arrowDoSth();
    },
    arrowDoSth2: () => {
        console.log(this.name);
    }
}
student.doSth(); // '若川'
student.arrowDoSth2(); // 'window'
复制代码
```

其实就是相当于箭头函数外的`this`是缓存的该箭头函数上层的普通函数的`this`。如果没有普通函数，则是全局对象（浏览器中则是`window`）。 也就是说无法通过`call`、`apply`、`bind`绑定箭头函数的`this`(它自身没有`this`)。而`call`、`apply`、`bind`可以绑定缓存箭头函数上层的普通函数的`this`。 比如：

```
var student = {
    name: '若川',
    doSth: function(){
        console.log(this.name);
        return () => {
            console.log('arrowFn:', this.name);
        }
    }
}
var person = {
    name: 'person',
}
student.doSth().call(person); // '若川'  'arrowFn:' '若川'
student.doSth.call(person)(); // 'person' 'arrowFn:' 'person'
```


# 11、generator

### Generator

`Generator` 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。`Generator` 函数将 JavaScript 异步编程带入了一个全新的阶段。

#### 声明

与函数声明类似，不同的是`function`关键字与函数名之间有一个星号，以及函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）。

```
function* gen(x){
 const y = yield x + 6;
 return y;
}
// yield 如果用在另外一个表达式中,要放在()里面
// 像上面如果是在=右边就不用加()
function* genOne(x){
  const y = `这是第一个 yield 执行:${yield x + 1}`;
 return y;
}
复制代码
```

#### 执行

```
const g = gen(1);
//执行 Generator 会返回一个Object,而不是像普通函数返回return 后面的值
g.next() // { value: 7, done: false }
//调用指针的 next 方法,会从函数的头部或上一次停下来的地方开始执行，直到遇到下一个 yield 表达式或return语句暂停,也就是执行yield 这一行
// 执行完成会返回一个 Object,
// value 就是执行 yield 后面的值,done 表示函数是否执行完毕
g.next() // { value: undefined, done: true }
// 因为最后一行 return y 被执行完成,所以done 为 true
复制代码
```

调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是`遍历器对象（Iterator Object）`。下一步，必须调用遍历器对象的`next`方法，使得指针移向下一个状态。

所以上面的回调函数又可以写成这样：

```
function *fetch() {
    yield ajax('aaa')
    yield ajax('bbb')
    yield ajax('ccc')
}
let gen = fetch()
let res1 = gen.next() // { value: 'aaa', done: false }
let res2 = gen.next() // { value: 'bbb', done: false }
let res3 = gen.next() // { value: 'ccc', done: false }
let res4 = gen.next() // { value: undefined, done: true } done为true表示执行结束
复制代码
```

由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

遍历器对象的`next`方法的运行逻辑如下。

（1）遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。

（2）下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。

（3）如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。

（4）如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

**`yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。**

怎么理解这句话？我们来看下面这个例子：

```
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}

var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
复制代码
```

由于`yield`没有返回值，所以（yield（x+1））执行后的值是`undefined`，所以在第二次执行`a.next()`是其实是执行的`2*undefined`，所以值是`NaN`，所以下面b的例子中，第二次执行`b.next()`时传入了12，它会当成第一次`b.next()`的执行返回值，所以b的例子中能够正确计算。**这里不能把next执行结果中的value值与yield返回值搞混了，它两不是一个东西**


# 11、weakmap/weakset

通过对强引用、弱引用、WeakMap 和 WeakSet 的深入理解，我们可以更好地管理内存并避免内存泄漏。弱引用的数据结构（如 WeakMap 和 WeakSet）在某些场景下能帮助我们在不影响对象生命周期的情况下，关联或跟踪对象。

在实际应用中，你可以根据具体场景选择使用 WeakMap 或 WeakSet：

-   当你需要为对象存储额外的信息（例如私有数据）时，可以使用 WeakMap。这样，在对象不再被引用时，它们将被垃圾回收器回收，同时从 WeakMap 中移除。
-   当你需要跟踪对象（例如检查对象是否已处理过）时，可以使用 WeakSet。这样，在对象不再被引用时，它们将被垃圾回收器回收，同时从 WeakSet 中移除。

在编写 JavaScript 代码时，请始终注意内存管理和垃圾回收机制，以确保程序的性能和稳定性。选择合适的引用类型和数据结构可以避免内存泄漏，提高程序的运行效率。

请注意，由于 WeakMap 和 WeakSet 中的元素只能是对象，它们不能用于存储原始类型（如字符串、数字或布尔值）的值。此外，由于它们的弱引用特性，你不能对 WeakMap 或 WeakSet 进行遍历操作。

在实际项目中，你可以根据实际需求，灵活地运用强引用和弱引用，以及 WeakMap 和 WeakSet 这两种数据结构，以便更好地解决内存泄漏问题，提高程序的性能和稳定性。


# 11、shallowReactive/shallowRef

https://juejin.cn/post/6975137341128540190

默认情况下，无论是通过 ref 还是 reactive 都是深度监听。 所以Vue3为我们提供了，shallowReactive进行非深度监听。 shallowReactive只会包装第一层的数据 默认情况它只能够监听数据的第一层。 如果想更改多层的数据， 你必须先更改第一层的数据。 然后在去更改其他层的数据。 这样视图上的数据才会发生变化。



# 11、typescript-record

# `record`

## 释义

> `Record<K,T>`
>
> Constructs an object type whose property keys are Keys and whose property values are Type. This utility can be used to map the properties of a type to another type.” —
>
> 构造一个对象类型，`Keys` 表示对象的属性键 、`Type` 表示对象的属性值，用于将一种类型属性映射到另一种类型

理解为：将 K 的每一个值都定义为 T 类型

## 源码

```
/**
 * Construct a type with a set of properties K of type T
 */
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
// keyof:将一个类型的属性名全部提取出来当做联合类型
```

源码也比较简单，即将K中的每个属性([P in K]),都转为T类型。常用的格式如下：

```
type proxyKType = Record<K,T>
复制代码
```

会将K中的所有属性值都转换为T类型，并将返回的新类型返回给proxyKType，K可以是联合类型、对象、枚举等

## 示例1

一条数据的状态值有：

-   已创建 created
-   已提交 submited
-   已删除 removed

```
type state = "created" | "submitted" | "removed"
复制代码
```

现在需要创建一个状态值映射对象, 这个兑现的成员是每一个状态值，成员的值是`String`类型。

如果我们手动描述类型的需要这么写：

```
interface StatesInterface {
  created:string
  submitted:string
  removed:string
}

export const states:StatesInterface = {
  created:'01',
  submitted:'02',
  removed:'03'
}
复制代码
```

使用`Record`后可以省去我们对`StatesInterface`接口的定义：

```
export const states:Record<state,string> = {
  created:'01',
  submitted:'02',
  removed:'03'
}
复制代码
```

## 示例2

比如现在要定义一个叫食物原料的对象，对象的值由我们规定的几种食物原料组成，我们可以定义原料类型，原料类型可以是巧克力，可可，蘑菇等，并且他们都是string类型，然后使用 Record 对他们进行包装。如下：

```
type Ingredient = "chocolate" | "peanuts" | "cocoa" | "marshmallow" | "cherry";

export const ingredients: Record<Ingredient, string> = {
  chocolate: "Chocolate",
  cocoa: "Cocoa Powder",
  cherry: "Cherry",
  marshmallow: "Marshmallow",
  peanuts: "Peanut Butter",
};
复制代码
```

当然也可以自己在第一个参数后追加额外的值，如：

```
type Ingredient = "chocolate" | "peanuts" | "cocoa" | "marshmallow" | "cherry";

export const ingredients: Record<Ingredient | "apple", string> = {
  chocolate: "Chocolate",
  cocoa: "Cocoa Powder",
  cherry: "Cherry",
  marshmallow: "Marshmallow",
  peanuts: "Peanut Butter",
  apple: "Apple"
};
复制代码
```

额外添加了一种原料叫 Apple

## 使用场景

通过了解 Record 的源码以及相关例子的实现，可以总结出以下常见的Record使用场景：

-   当我们想限制对象的属性时
-   转换现有类型的属性并将其值转换为其它类型时（结合keyof使用）

Record是一个有用和简要的工具类型，可以让你的代码更健壮，在编译时更容易捕获错误，并且使IDE更加容易的标记错误。


# 11、requestAnimationFrame

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d57664d52908418689d6e0f586865e54~tplv-k3u1fbpfcp-zoom-1.image)

两者区别在于`回调触发`与`页面渲染`之间的间隔，`requestAnimationFrame`与`帧变化`是紧密联系的，回调触发与页面渲染之间是几乎是连续执行的，而定时器与页面渲染之间会有间隔，这样子带来的性能问题是什么

`setInterval`某些帧的触发时机并不稳定，导致动画卡顿，原因就是定时器设置的延迟时间，不是回调函数执行的延迟时间，而是加入事件队列的延迟时间，加入事件队列后需要等待主线程空闲，再将事件队列中的任务加入执行栈执行，由于定时器不能稳定触发，所以无法保证每一帧能够`准时`渲染，而`requestAnimationFrame`与`帧变化`紧密联系，是在每一帧渲染前触发，所以`能够在合适的时间内重复执行动画帧，提升渲染性能`

  


作者：前端没死  
链接：https://juejin.cn/post/7208479235132342327  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

# 11、tcp/udp

#### TCP定义
TCP是一个面向连接的`传输层`协议，是可靠的、基于字节流的，具有`超时重传`、`拥塞控制`的机制
    
    面向连接： 需要3次握手建立连接
    可靠性：TCP具有确认应答`ACK`和`序列号`来实现可靠传输
    字节流：UDP传输的一个UDP报文发送一个完整的用户消息；TCP则是将用户消息拆分为多个TCP报文段

#### UDP定义
UDP是一个无连接的`传输层`协议

#### TCP、UDP的区别
-   `TCP` 是面向连接的，`UDP` 是无连接的即发送数据前不需要先建立链接
-   `TCP` 是可靠传输，保证数据正确且有序；`UDP`是不可靠的，可能丢包或乱序
-   `TCP` 是面向字节流，`UDP` 面向报文，并且网络出现拥塞不会使得发送速率降低
-   `TCP` 首部开销大，最小`20`字节最大`60`字节，而 `UDP` 首部开销小，仅`8`字节
-   `TCP` 只能是 `1 对 1` 的，`UDP` 支持 `1 对 1`,`1 对多`；


#### TCP的可靠性
-   序列号：`TCP`给每一个包一个序号，保证接收端的按序接收；
-   确认应答ACK：接收端收到包就会回一个相应的确认ACK，如果发送端在一个往返时延内未收到确认就会重传；
-   流量控制：通过控制发送者的发送速度来缓解接收者的拥塞；
-   拥塞控制：当网络出现拥塞的时候，`TCP`能够减小向网络注入数据的速率和数量，缓解拥塞，避免网络中的路由器或链路不致过载，导致数据丢失。从而保证了TCP传输的可靠性；
-   通过三次握手，四次挥手建立和关闭连接的**连接管理**保证了端对端的通信可靠性。
-   TCP还使用了**滑动窗口控制**提高了数据传输效率
-   https://mp.weixin.qq.com/s/pkhabqv2salM1MpBTH4D6A

#### TCP三次握手
-   第一步：客户端发送`SYN`报文到服务端发起握手
-   第二步：服务端收到`SYN`报文之后回复`SYN`和`ACK`报文给客户端
-   第三步：客户端收到`SYN`和`ACK`，向服务端发送一个`ACK`报文

    `三次握手的意义`: 客户端和服务端都需要直到各自可收发，因此需要三次握手
    `为什么 TCP 建立连接需要三次握手，而不是两次`: 因为这是为了防止出现失效的连接请求报文段被服务端接收的情况，从而产生错误
    
#### TCP四次挥手
-   第一次挥手，客户端发送`FIN`给服务端
-   第二次挥手，服务端回复`ACK`给客户端，服务端还可以继续向客户端发送数据（若数据没有发送完）
-   第三次挥手，服务端发送`FIN`给客户端
-   第四次挥手，客户端回复`ACK`给服务端，客户端经过 `2MSL（最大报文发送时长）` 的时间后断开，服务端接收到了客户端发出的`ACK`后立刻断开了到客户端的连接

###### 四次挥手结束等待 2MSL 的意义

-   虽然按道理，四个报文都发送完毕，就可以立即断开，但是我们必须假设网络是不可靠的，有可以最后一个`ACK`丢失。
-   如果最后一个 `ACK` 丢失了，那么服务端没有收到 `ACK` 就会发起重传；再次发送 `FIN` 给客户端；
-   客户端收到重传的 `FIN` 后，会重发 `ACK` 并重新等待 `2MSL` 的时间来确保服务端收到了自己的 `ACK`；

总结：
-  1 个 `MSL` 确保`第四次挥手`中`主动关闭方`最后的 `ACK` 报文最终能达到对端
-  1 个 `MSL` 确保对端没有收到 `ACK` 重传的 `FIN` 报文可以到达


    


# 11、http/https

https://juejin.cn/post/6994629873985650696 
https://juejin.cn/post/6844903559952089102#heading-4

### HTTP 1.0(1996年)

-   任意数据类型都可以发送
-   有GET、POST、HEAD三种方法
-   无法复用TCP连接(长连接)
-   有丰富的请求响应头信息。以header中的`Last-Modified`/`If-Modified-Since`和`Expires`作为缓存标识

### HTTP 1.1(1997年)

-   引入更多的请求方法类型`PUT`、`PATCH`、`DELETE`、`OPTIONS`、`TRACE`、`CONNECT`
-   引入长连接，就是TCP连接默认不关闭，可以被多个请求复用，通过请求头connection:keep-alive设置
-   引入管道连接机制，可以在同一TCP连接里，`同时发送`多个请求
-   强化了缓存管理和控制`Cache-Control`、`ETag`/`If-None-Match`
-   支持分块响应，断点续传，利于大文件传输，能过请求头中的`Range`实现
-   使用了`虚拟网络`，在一台物理服务器上可以存在多个虚拟主机，并且共享一个IP地址

**缺点**：主要是连接缓慢，服务器只能按顺序响应，如果某个请求花了很长时间，就会出现请求队头阻塞

虽然出了很多优化技巧：为了增加并发请求，做域名拆分、资源合并、精灵图、资源预取...等等

最终为了推进从协议上进行优化，Google跳出来，推出`SPDY`协议

### HTTP 2.0(2015年)

说出http2中至少三个新特性？

-   使用新的`二进制协议`，不再是纯文本，避免文本歧义，缩小了请求体积
-   `多路复用`，同域名下所有通信都是在单链接(双向数据流)完成，提高连接的复用率，在拥塞控制方面有更好的能力提升
-   使用`HPACK算法将头部压缩`，用`哈夫曼编码`建立索表，传送索引大大节约了带宽
-   允许`服务端主动推送`数据给客户端
-   增加了安全性，使用HTTP 2.0，要求必须至少TLS 1.2
-   使用虚拟的流传输消息，解决了应用层的队头阻塞问题

**缺点**

-   TCP以及TCP+TLS建立连接的延时，HTTP2使用TCP协议来传输的，而如果使用HTTPS的话，还需要TLS协议进行安全传输，而使用TLS也需要一个握手过程，在传输数据之前，导致我们花掉3~4个RTT
-   TCP的队头阻塞并没有彻底解决。在HTTP2中，多个请求跑在一个TCP管道中，但当HTTP2出现丢包时，整个TCP都要开始等待重传，那么就会阻塞该TCP连接中的所有请求

### HTTP 3.0/QUIC

由于HTTP 2.0依赖于TCP，TCP有什么问题那HTTP2就会有什么问题。最主要的还是队头阻塞，在应用层的问题解决了，可是在TCP协议层的队头阻塞还没有解决。

TCP在丢包的时候会进行重传，前面有一个包没收到，就只能把后面的包放到缓冲区，应用层是无法取数据的，也就是说HTTP2的多路复用并行性对于TCP的丢失恢复机制不管用，因此丢失或重新排序的数据都会导致交互挂掉

为了解决这个问题，Google又发明了`QUIC协议`

并在2018年11月将QUIC正式改名为`HTTP 3.0`

**特点**：

-   在传输层直接干掉TCP，用`UDP`替代
-   实现了一套新的`拥塞控制算法`，彻底解决TCP中队头阻塞的问题
-   实现了类似TCP的`流量控制`、传输可靠性的功能。虽然UDP不提供可靠性的传输，但QUIC在UDP的基础之上增加了一层来保证数据可靠性传输。它提供了数据包重传、拥塞控制以及其他一些TCP中存在的特性
-   实现了`快速握手`功能。由于QUIC是基于UDP的，所以QUIC可以实现使用0-RTT或者1-RTT来建立连接，这意味着QUIC可以用最快的速度来发送和接收数据。
-   集成了TLS加密功能。目前QUIC使用的是TLS1.3


HTTPS = HTTP+ SSL/TLS

`TLS(Transport Layer Security传输层安全性)` 是 `SSL(Secure Socket Layer安全套接字层)` 的后续版本，它们是用于在互联网两台计算机之间用于`身份验证`和`加密`的一种协议

    机密性：对称加密 AES + 非对称加密 RSA
    完整性：摘要算法
    身份认证：数字证书
    https://juejin.cn/post/6844904089495535624#heading-13
    
  ## HTTPS 的性能优化

### 访问速度优化

1.  `会话复用`，上面说了，复用session可以减少 CPU 消耗，因为不需要进行非对称密钥交换的计算。可以提升访问速度，不需要进行完全握手阶段二，节省了一个 RTT 和计算耗时。
1.  使用 `SPDY` 或者 `HTTP2`。SPDY 最大的特性就是多路复用，能将多个 HTTP 请求在同一个连接上一起发出去，不像目前的 HTTP 协议一样，只能串行地逐个发送请求。Pipeline 虽然支持多个请求一起发送，但是接收时依然得按照顺序接收，本质上无法解决并发的问题。HTTP2支持多路复用，有同样的效果。
1.  设置`HSTS`，服务端返回一个 HSTS 的 http header，浏览器获取到 HSTS 头部之后，在一段时间内，不管用户输入www.baidu.com还是http://www.baidu.com ，都会默认将请求内部跳转成https://www.baidu.com。Chrome, firefox, ie 都支持了 HSTS。
1.  `Nginx`设置`Ocsp stapling`。Ocsp 全称在线证书状态检查协议 (rfc6960)，用来向 CA 站点查询证书状态，比如是否撤销。通常情况下，浏览器使用 OCSP 协议发起查询请求，CA 返回证书状态内容，然后浏览器接受证书是否可信的状态。这个过程非常消耗时间，因为 CA 站点有可能在国外，网络不稳定，RTT 也比较大。如果不需要查询则可节约时间。
1.  `False start`。简单概括 False start 的原理就是在 clientkeyexchange 发出时将应用层数据一起发出来，能够节省一个 RTT。

### 计算性能优化

1.  优先使用 ECC椭圆加密算术
1.  使用最新版的 OpenSSL
1.  TLS 远程代理计算
1.  硬件加速方案

### HTTP1 和 HTTP2

-   HTTP2是一个`二进制协议`，HTTP1是`超文本协议`，传输的内容都不是一样的
-   HTTP2报头压缩，可以使用HPACK进行`头部压缩`，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小
-   HTTP2`服务端推送`(Server push)，允许服务器预先将网页所需要的资源push到浏览器的内存当中
-   HTTP2遵循`多路复用`，代替同一域名下的内容，只建立一次连接，HTTP1.x不是，对域名有6~8个连接限制
-   HTTP2引入`二进制数据帧`和`流`的概念，其中帧对数据进行顺序标识，这样浏览器收到数据之后，就可以按照序列对数据进行合并，而不会出现合并后数据错乱的情况，同样是因为有了序列，服务器就可以并行的传输数据，这就是流所做的事情。HTTP2对同一域名下所有请求都是基于流的，也就是说同一域名下不管访问多少文件，只建立一次连接

## HTTP 和 HTTPS 的区别

-   HTTP是`明文传输`，不安全的，HTTPS是`加密传输`，安全的多
-   HTTP标准端口是`80`，HTTPS标准端口是`443`
-   HTTP不用认证证书`免费`，HTTPS需要认证证书`要钱`
-   `连接方式不同`，HTTP三次握手，HTTPS中TLS1.2版本7次，TLS1.3版本6次
-   HTTP在OSI网络模型中是在`应用层`，而HTTPS的TLS是在`传输层`
-   HTTP是`无状态`（每一次请求都是独立的，请求结束不会记录连接的任何信息）的，HTTPS是`有状态`（session复用）的


### 数字证书

黑客冒充某网站给客户端一个假的公钥，如果你拿到了假的公钥，混合加密就完全失效了。所以，为了解决公钥的信任问题，需要引入“数字证书”。

服务器需提供：

-   数据内容（会话密钥加密）
-   数字签名（服务器私钥加密后的摘要）
-   数字证书（CA私钥加密的服务器公钥）

客户端收到后：

-   先用CA公钥解密证书，把服务器公钥拿出来
-   使用服务器公钥解密数字签名，得到摘要
-   使用摘要算法对内容进行计算，算出的摘要与刚才解密出的摘要进行对比，如果一模一样，说明数据是完整的，没被篡改的

**身份认证**：使用CA颁发的数字证书来证明这个公钥确实是某某服务器的公钥。

### 混合加密

TLS里使用了”混合加密“方式：

-   在通信刚开始的时候使用**非对称加密**算法，比如 RSA、ECDHE，首先解决密钥交换的问题；【**用随机数产生对称加密算法使用的“会话密钥”（session key），再用公钥加密。因为会话密钥很短，通常只有 16 字节或 32 字节，所以慢一点也无所谓。对方拿到密文后用私钥解密，取出会话密钥。这样，双方就实现了对称密钥的安全交换。** 】
-   后续全都使用**对称加密**进行通信。





# 11、typecript高级用法

https://juejin.cn/post/7046393423263170567#heading-7

映射(Record)

Partial(转化可选参数)

Required（转换必须参数）

Pick（选择-返回新类型）

Readonly (属性转换为只读)

Exclude(排除-返回新类型)

Omit (省略)

ReadonlyArray（只读数组）

# 11、ref和reactive

### 回答思路

0.  两者概念
0.  两者使用场景
0.  两者异同
0.  使用细节
0.  原理

* * *

### 回答范例

0.  `ref`接收内部值（inner value）返回响应式`Ref`对象，`reactive`返回响应式代理对象
0.  从定义上看`ref`通常用于处理单值的响应式，`reactive`用于处理对象类型的数据响应式
0.  两者均是用于构造响应式数据，但是`ref`主要解决原始值的响应式问题
0.  ref返回的响应式数据在JS中使用需要加上`.value`才能访问其值，在视图中使用会自动脱ref，不需要`.value`；ref可以接收对象或数组等非原始值，但内部依然是`reactive`实现响应式；reactive内部如果接收Ref对象会自动脱ref；使用展开运算符(...)展开reactive返回的响应式对象会使其失去响应性，可以结合toRefs()将值转换为Ref对象之后再展开。
0.  reactive内部使用Proxy代理传入对象并拦截该对象各种操作（trap），从而实现响应式。ref内部封装一个RefImpl类，并设置get value/set value，拦截用户对值的访问，从而实现响应式。

-   `reactive` 一般用于对象/数组类型的数据，都不需要使用 `.value`；
-   `ref`一般用于基础数据类型的数据，在 JS 中读取和修改时，需要使用 `.value`，在模版中使用时则不需要；
-   `reactive` 可以修改深层属性值，并保持响应；
-   `reactive` 返回值和源对象不同；
-   `reactive`的属性值可以是 `ref`值；
-   `ref`本质也是 `reactive`，`ref(obj)`等价于 `reactive({value: obj})`。

-   `ref`是通过一个中间对象`RefImpl`持有数据，并通过重写它的set和get方法实现数据劫持的，本质上依旧是通过Object.defineProperty对`RefImpl`的`value`属性进行劫持。

-   `reactive`则是通过Proxy进行劫持的。Proxy无法对基本数据类型进行操作，进而导致`reactive`在面对基本数据类型时的束手无策

https://juejin.cn/post/7211055301205934138#heading-2

### 总结和用法

1.  `ref`可以存储原始类型，而`reactive`不能。
1.  `ref`需要通过`<ref>.value`访问数据，而`reactive()`可以直接用作常规对象。
1.  可以重新分配一个全新的对象给`ref`的`value`属性，而`reactive()`不能。
1.  `ref`类型为`Ref<T>`，而`reactive`返回的反应类型为原始类型本身。
1.  基于第四条，`ref`可以自身管理依赖而`reactive`则借助全局变量以键值对的形式进行管理。
1.  `watch`默认只观察`ref`的`value`，而对`reactive`则执行深度监听。
1.  `ref`默认会用` reactive  `对象类型的原始值进行深层响应转换。




# 11、微前端

https://juejin.cn/post/7069566144750813197

## 为什么选择qiankun
市面框架对比：single-spa，MicroApp，EMOP
综合以上方案对比之后，我们确定采用了 `qiankun` 特定中心路由基座式的开发方案，原因如下：

-   保证技术栈统一 Vue、微应用之间完全独立，互不影响。
-   友好的“微前端方案“，与技术栈无关接入简单、像iframe一样简单
-   改造成本低，对现有工程侵入度、业务线迁移成本也较低。
-   和原有开发模式基本没有不同，开发人员学习成本较低。
-   qiankun 的微前端有 3 年使用场景以及 Issue 问题解决积累，社区也比较活跃，在踩坑的路上更容易自救～

#### 主应用
1. 注册微应用：registerMicroApps、loadMicroApps
```
import LayoutPage from "@/layout/index";
import {
  registerMicroApps,
  start,
  addGlobalUncaughtErrorHandler,
} from "qiankun";
import { Microconfig } from "@/registerMicroAppsConfig";

// 注册微应用
registerMicroApps(Microconfig, {
  // qiankun 生命周期钩子 - 微应用加载前
  beforeLoad: (app: any) => {
    console.log("before load", app.name);
    return Promise.resolve();
  },
  // qiankun 生命周期钩子 - 微应用挂载后
  afterMount: (app: any) => {
    console.log("after mount", app.name);
    return Promise.resolve();
  },
});

// 启动 qiankun
start();

export default function IndexPage({ children }: any) {
  return (
    <LayoutPage>
      <div>{children}</div>
      {/* 增加容器，用于显示微应用 */}
      <div id="subContainer"></div>
    </LayoutPage>
  );
}
```
2.添加全局异常捕获
```
// 添加全局异常捕获
addGlobalUncaughtErrorHandler((handler) => {
  console.log("异常捕获", handler);
});
```

#### 微应用
1. webpack public-path改造
```
if (window.__POWERED_BY_QIANKUN__) { __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__; }
```

2. main.js入口文件暴露bootstrap，mount，unmount函数

3.打包配置修改（`vue.config.js`）
```
1. 允许跨域
2. output修改
 const path = require("path");
 const { name } = require("./package");

 function resolve(dir) {
   return path.join(__dirname, dir);
 }

 module.exports = {
   filenameHashing: true,
   lintOnSave: process.env.NODE * ENV !== "production",
   runtimeCompiler: true,
   productionSourceMap: false,
   devServer: {
     hot: true,
     disableHostCheck: true,
     // 修改默认端口，和注册时一直
     port: 8001,
     overlay: {
       warnings: false,
       errors: true,
     },
     // 解决主应用加载子应用出现跨域问题
     headers: {
       "Access-Control-Allow-Origin": "*",
     },
   },
   // 自定义 webpack 配置
   configureWebpack: {
     resolve: {
       alias: {
         "@": resolve("src"),
       },
     },
     // 让主应用能正确识别微应用暴露出来的一些信息
     output: {
       library: `${name}-[name]`,
       libraryTarget: "umd", // 把子应用打包成 umd 库格式
       jsonpFunction: `webpackJsonp*${name}`,
     },
   },
 };
```
#### 应用间通信

1.props 传值 - vuex

2.initGlobalState

    - 主应用中声明全局状态
    // 全局状态
    const state = {
      id: 'main_主应用',
    };
    // 初始化 state
    const actions: MicroAppStateActions = initGlobalState(state);
    // 监听状态变更
    actions.onGlobalStateChange((state, prev) => {
      // state: 变更后的状态; prev 变更前的状态
      console.log(state, prev);
    });
    
    - 微应用获取通信,同样在 `mount` 生命周期中获取
    export async function mount(props) {
      console.log('initGlobalState传值',props)
      render(props);
    }
    
    function render(props) {
      props.onGlobalStateChange &&
        props.onGlobalStateChange(
          (value, prev) => console.log(`[onGlobalStateChange - ${props.name}]:`, value, prev),
          true,
        );
      // 为了演示效果明显增加定时器
        setTimeout(() =>{
          props.setGlobalState &&
          props.setGlobalState({
            id: `${props.name}_子应用`
          });
        },3000)
    }


#### 最佳实践
1. 多个微应用间keep-alive

        **registerMicroApps模式下，为什么切换路由会导致应用重载？**
        -   URL 改变时应用匹配切换，路由的切换会导致应用的卸载与加载
            -   例：A 到 B， 触发A unmount ⇒ 判断 B 是否加载过，已加载则触发 mount，未加载则触发 bootstrap ⇒ mount
        -   如果子应用挂载在内部路由，路由跳转也将触发应用的重载
        -   应用切换导致重载，导致组件状态丢失，为了保持应用实例不被加载，我们需要手动的控制应用的注册与销毁

    - 注册多个容器，通过display控制显隐
    
    - loadMicroApp

2. 样式隔离

        qiankun是默认开启沙箱隔离的，默认情况下沙箱可以确保单实例场景子应用之间的样式隔离，但是无法确保主应用跟子应用、或者多实例场景的子应用样式隔离
    - css命名遵循ABEM规范
    - **postcss** 统一给所有的class增加前缀
    ```
    module.exports = ctx => {
    return {
        plugins: [
            require('postcss-plugin-namespace')('#your-prefix', {
                ignore: ['html', /body/]
                })
            ]
        }
    }
    ```

微前端就是在路由变化的时候，加载对应应用的代码，并在容器内跑起来。

qiankun、wujie、micro-app 的区别主要还是实现容器（或者叫沙箱）上有区别，比如 qiankun 是 function + proxy + with，micro-app 是 web components，而 wujie 是 web components 和 iframe。

https://juejin.cn/post/7148075486403362846

https://juejin.cn/post/7202246519080304697#heading-2

 


# 11、computed原理
https://juejin.cn/post/6844903678533451783

-   当组件初始化的时候，`computed` 和 `data` 会分别建立各自的响应系统，`Observer`遍历 `data` 中每个属性设置 `get/set` 数据拦截

-   初始化 `computed` 会调用 `initComputed` 函数

    1.  注册一个 `watcher` 实例，并在内实例化一个 `Dep` 消息订阅器用作后续收集依赖（比如渲染函数的 `watcher` 或者其他观察该计算属性变化的 `watcher` ）
    1.  调用计算属性时会触发其`Object.defineProperty`的`get`访问器函数
    1.  调用 `watcher.depend()` 方法向自身的消息订阅器 `dep` 的 `subs` 中添加其他属性的 `watcher`
    1.  调用 `watcher` 的 `evaluate` 方法（进而调用 `watcher` 的 `get` 方法）让自身成为其他 `watcher` 的消息订阅器的订阅者，首先将 `watcher` 赋给 `Dep.target`，然后执行 `getter` 求值函数，当访问求值函数里面的属性（比如来自 `data`、`props` 或其他 `computed`）时，会同样触发它们的 `get` 访问器函数从而将该计算属性的 `watcher` 添加到求值函数中属性的 `watcher` 的消息订阅器 `dep` 中，当这些操作完成，最后关闭 `Dep.target` 赋为 `null` 并返回求值函数结果。

-   当某个属性发生变化，触发 `set` 拦截函数，然后调用自身消息订阅器 `dep` 的 `notify` 方法，遍历当前 `dep` 中保存着所有订阅者 `wathcer` 的 `subs` 数组，并逐个调用 `watcher` 的 `update` 方法，完成响应更新。

```
class Watcher {
  constructor (
    vm: Component,
    expOrFn: string | Function,
    cb: Function,
    options?: ?Object,
    isRenderWatcher?: boolean
  ) {
    if (options) {
      this.computed = !!options.computed
    } 

    if (this.computed) {
      this.value = undefined
      this.dep = new Dep()
    } else {
      this.value = this.get()
    }
  }
  
  get () {
    pushTarget(this)
    let value
    const vm = this.vm
    try {
      value = this.getter.call(vm, vm)
    } catch (e) {
      
    } finally {
      popTarget()
    }
    return value
  }
  
  update () {
    if (this.computed) {
      if (this.dep.subs.length === 0) {
        this.dirty = true
      } else {
        this.getAndInvoke(() => {
          this.dep.notify()
        })
      }
    } else if (this.sync) {
      this.run()
    } else {
      queueWatcher(this)
    }
  }

  evaluate () {
    if (this.dirty) {
      this.value = this.get()
      this.dirty = false
    }
    return this.value
  }

  depend () {
    if (this.dep && Dep.target) {
      this.dep.depend()
    }
  }
}
```
# 11、路由守卫
https://juejin.cn/post/7106128370832064543

全局守卫
router.beforeEach
router.beforeResolve
router.afterEach

路由独享
beforeEnter

组件内的守卫
beforeRouteEnter
beforeRouteUpdate
beforeRouteLeave

将路由导航、`keep-alive`、和组件生命周期钩子结合起来的，触发顺序，假设是从a组件离开，第一次进入b组件：

1.  `beforeRouteLeave`:路由组件的组件离开路由前钩子，可取消路由离开。
1.  `beforeEach`: 路由全局前置守卫，可用于登录验证、全局路由loading等。
1.  `beforeEnter`: 路由独享守卫
1.  `beforeRouteEnter`: 路由组件的组件进入路由前钩子。
1.  `beforeResolve`:路由全局解析守卫
1.  `afterEach`:路由全局后置钩子
1.  `beforeCreate`:组件生命周期，不能访问`this`。
1.  `created`:组件生命周期，可以访问`this`，不能访问dom。
1.  `beforeMount`:组件生命周期
1.  `deactivated`: 离开缓存组件a，或者触发a的`beforeDestroy`和`destroyed`组件销毁钩子。
1.  `mounted`:访问/操作dom。
1.  `activated`:进入缓存组件，进入a的嵌套子组件(如果有的话)。
1.  执行beforeRouteEnter回调函数next。


# 11、css选择器优先级

> 内联 > ID选择器 > 类选择器 > 标签选择器。

但是，浏览器具体的优先级算法是怎样的？可能还有些人不知道 。《CSS REFACTORING》 中提到了算法的过程 。

A specificity is determined by plugging numbers into (a, b, c, d):

1.  If the styles are applied via the style attribute, a=1; otherwise, a=0.
1.  b is equal to the number of ID selectors present.
1.  c is equal to the number of class selectors, attribute selectors, and pseudoclasses present.
1.  d is equal to the number of type selectors and pseudoelements present.

翻译过来就是

优先级是由 `A` 、`B`、`C`、`D` 的值来决定的，其中它们的值计算规则如下：

1.  如果存在内联样式，那么 `A = 1`, 否则 `A = 0`;
1.  `B` 的值等于 `ID选择器` 出现的次数;
1.  `C` 的值等于 `类选择器` 和 `属性选择器` 和 `伪类` 出现的总次数;
1.  `D` 的值等于 `标签选择器` 和 `伪元素` 出现的总次数 。

  
important > 内联样式

https://juejin.cn/post/6844903709772611592
# 11、浏览器
https://juejin.cn/post/6844904040346681358
# 11、css-position
https://juejin.cn/post/7078476624831709192#heading-3

## 一、static

static 是 position 的默认值，就是没有定位，元素处于现在正常的文档流中

## 二、relative

relative 是相对定位，指的是给元素设置相对于自己原本位置的定位，元素并不脱离文档流，因此元素原本的位置会被保留，其他的元素位置不会受到影响

## 三、absolute

absolute 是绝对定位，是的指让元素相对于 static 定位之外的第一个父元素进行定位，分为两种情况

1.  设置了 absolute 的元素如果存在有父元素设置了 position 属性为 relative 或者absolute，此时该元素就以这些父元素来进行定位
1.  如果没有设置了 position 属性为 relative 或者 absolute 父元素，则此时相对于 body 进行定位

absolute 是生成的绝对定位的元素，是会脱离了文本流的，即在文档中已经不占据位置，常用于结合 relative 来使用

## 四、fixed

fixed 是一种特殊的绝对定位，也会脱离文档流，只不过 fixed 的元素是固定相对与 body 来定位的

## 五、sticky

sticky 是粘性定位，可以说是相对定位 relative 和固定定位 fixed 的结合体，一开始是没有脱离文档流的，但是当元素距离其父元素的距离达到 sticky 粘性定位的要求时 position:sticky 这时的效果相当于 fixed 定位，固定到适当位置，脱离了文档流

## 六、inherit

inherit 就是继承父元素的 position 属性  
、
# 11、bfc

BFC 即块级格式上下文，根据盒模型可知，每个元素都被定义为一个矩形盒子，然而盒子的布局会受到**尺寸，定位，盒子的子元素或兄弟元素，视口的尺寸**等因素决定，所以这里有一个浏览器计算的过程，计算的规则就是由一个叫做**视觉格式化模型**的东西所定义的，BFC 就是来自这个概念，它是 CSS 视觉渲染的一部分，**用于决定块级盒的布局及浮动相互影响范围的一个区域**。

BFC 具有一些特性：

1.  块级元素会在垂直方向一个接一个的排列，和文档流的排列方式一致。
1.  在 BFC 中上下相邻的两个容器的 `margin`  会重叠，创建新的 BFC 可以避免外边距重叠。
1.  计算 BFC 的高度时，需要计算浮动元素的高度。
1.  BFC 区域不会与浮动的容器发生重叠。
1.  BFC 是独立的容器，容器内部元素不会影响外部元素。
1.  每个元素的左 `margin`  值和容器的左 `border`  相接触。

利用这些特性，我们可以解决以下问题：

-   利用 `4`  和 `6` ，我们可以实现三栏（或两栏）自适应布局。
-   利用 `2` ，我们可以避免 `margin`  重叠问题。
-   利用 `3` ，我们可以避免高度塌陷。

创建 BFC 的方式：

-   绝对定位元素（`position` 为 `absolute` 或 `fixed` ）。
-   行内块元素，即 `display` 为 `inline-block` 。
-   `overflow` 的值不为 `visible` 。

  


作者：vortesnail  
链接：https://juejin.cn/post/7061588533214969892  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

margin重叠问题：
https://juejin.cn/post/7033349421731741727
# 58、微前端

https://juejin.cn/post/7069566144750813197

## 为什么选择qiankun
市面框架对比：single-spa，MicroApp，EMOP
综合以上方案对比之后，我们确定采用了 `qiankun` 特定中心路由基座式的开发方案，原因如下：

-   保证技术栈统一 Vue、微应用之间完全独立，互不影响。
-   友好的“微前端方案“，与技术栈无关接入简单、像iframe一样简单
-   改造成本低，对现有工程侵入度、业务线迁移成本也较低。
-   和原有开发模式基本没有不同，开发人员学习成本较低。
-   qiankun 的微前端有 3 年使用场景以及 Issue 问题解决积累，社区也比较活跃，在踩坑的路上更容易自救～

#### 主应用
1. 注册微应用：registerMicroApps、loadMicroApps
```
import LayoutPage from "@/layout/index";
import {
  registerMicroApps,
  start,
  addGlobalUncaughtErrorHandler,
} from "qiankun";
import { Microconfig } from "@/registerMicroAppsConfig";

// 注册微应用
registerMicroApps(Microconfig, {
  // qiankun 生命周期钩子 - 微应用加载前
  beforeLoad: (app: any) => {
    console.log("before load", app.name);
    return Promise.resolve();
  },
  // qiankun 生命周期钩子 - 微应用挂载后
  afterMount: (app: any) => {
    console.log("after mount", app.name);
    return Promise.resolve();
  },
});

// 启动 qiankun
start();

export default function IndexPage({ children }: any) {
  return (
    <LayoutPage>
      <div>{children}</div>
      {/* 增加容器，用于显示微应用 */}
      <div id="subContainer"></div>
    </LayoutPage>
  );
}
```
2.添加全局异常捕获
```
// 添加全局异常捕获
addGlobalUncaughtErrorHandler((handler) => {
  console.log("异常捕获", handler);
});
```

#### 微应用
1. webpack public-path改造
```
if (window.__POWERED_BY_QIANKUN__) { __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__; }
```

2. main.js入口文件暴露bootstrap，mount，unmount函数

3.打包配置修改（`vue.config.js`）
```
1. 允许跨域
2. output修改
 const path = require("path");
 const { name } = require("./package");

 function resolve(dir) {
   return path.join(__dirname, dir);
 }

 module.exports = {
   filenameHashing: true,
   lintOnSave: process.env.NODE * ENV !== "production",
   runtimeCompiler: true,
   productionSourceMap: false,
   devServer: {
     hot: true,
     disableHostCheck: true,
     // 修改默认端口，和注册时一直
     port: 8001,
     overlay: {
       warnings: false,
       errors: true,
     },
     // 解决主应用加载子应用出现跨域问题
     headers: {
       "Access-Control-Allow-Origin": "*",
     },
   },
   // 自定义 webpack 配置
   configureWebpack: {
     resolve: {
       alias: {
         "@": resolve("src"),
       },
     },
     // 让主应用能正确识别微应用暴露出来的一些信息
     output: {
       library: `${name}-[name]`,
       libraryTarget: "umd", // 把子应用打包成 umd 库格式
       jsonpFunction: `webpackJsonp*${name}`,
     },
   },
 };
```
#### 应用间通信

1.props 传值 - vuex

2.initGlobalState

    - 主应用中声明全局状态
    // 全局状态
    const state = {
      id: 'main_主应用',
    };
    // 初始化 state
    const actions: MicroAppStateActions = initGlobalState(state);
    // 监听状态变更
    actions.onGlobalStateChange((state, prev) => {
      // state: 变更后的状态; prev 变更前的状态
      console.log(state, prev);
    });
    
    - 微应用获取通信,同样在 `mount` 生命周期中获取
    export async function mount(props) {
      console.log('initGlobalState传值',props)
      render(props);
    }
    
    function render(props) {
      props.onGlobalStateChange &&
        props.onGlobalStateChange(
          (value, prev) => console.log(`[onGlobalStateChange - ${props.name}]:`, value, prev),
          true,
        );
      // 为了演示效果明显增加定时器
        setTimeout(() =>{
          props.setGlobalState &&
          props.setGlobalState({
            id: `${props.name}_子应用`
          });
        },3000)
    }


#### 最佳实践
1. 多个微应用间keep-alive

        **registerMicroApps模式下，为什么切换路由会导致应用重载？**
        -   URL 改变时应用匹配切换，路由的切换会导致应用的卸载与加载
            -   例：A 到 B， 触发A unmount ⇒ 判断 B 是否加载过，已加载则触发 mount，未加载则触发 bootstrap ⇒ mount
        -   如果子应用挂载在内部路由，路由跳转也将触发应用的重载
        -   应用切换导致重载，导致组件状态丢失，为了保持应用实例不被加载，我们需要手动的控制应用的注册与销毁

    - 注册多个容器，通过display控制显隐
    
    - loadMicroApp

2. 样式隔离

        qiankun是默认开启沙箱隔离的，默认情况下沙箱可以确保单实例场景子应用之间的样式隔离，但是无法确保主应用跟子应用、或者多实例场景的子应用样式隔离
    - css命名遵循ABEM规范
    - **postcss** 统一给所有的class增加前缀
    ```
    module.exports = ctx => {
    return {
        plugins: [
            require('postcss-plugin-namespace')('#your-prefix', {
                ignore: ['html', /body/]
                })
            ]
        }
    }
    ```

微前端就是在路由变化的时候，加载对应应用的代码，并在容器内跑起来。

qiankun、wujie、micro-app 的区别主要还是实现容器（或者叫沙箱）上有区别，比如 qiankun 是 function + proxy + with，micro-app 是 web components，而 wujie 是 web components 和 iframe。

https://juejin.cn/post/7148075486403362846

https://juejin.cn/post/7202246519080304697#heading-2

 
# 11、跟我聊v8引擎 背了垃圾回收他不问

# 12、堆和栈

# 12、说说dom的生成和渲染


# 12、ES6有哪些新增属性
# 12、闭包
# 12、有哪几种路由守卫？组件路由守卫和组件生命周期先后？
# 12、问我Position属性有哪些 区别是啥
# 12、qiankun配置多个子应用为什么可以只用一个container
# 13、qiankun为啥之前web端每个子应用都单独挂载一个容器啊

# 14、watcher和computed的区别了么

# 15、computed是啥原理啊

# 16、electron-updater都能问个原理出来 我也不知道我说的算不算原理 我说打包生成一个yml文件和包一起放在服务器，项目配置好服务器地址，updater就会自动去比较版本号，如果是高版本 触发更新


请简述JSX和Virtual DOM的工作原理，并解释它们在React中的作用
怎么把vue中的template转换成virtual DOM的
vue中的computed是如何实现的
请介绍一下Webpack的构建流程
请介绍一下webpack常用的优化方式
在您的前端项目中，您是如何处理浏览器兼容性问题的？请给出一些例子。
请介绍一下桌面端应用如何实现更新，怎么做到无感知，有没有最佳实践。
请介绍一下您对于前端性能优化的理解，并分享一些您在项目中采用的优化方法。
在智能工单系统中，你采用了哪些技术方案来优化图片和音频文件的上传效率？效果如何？
在开发商场智能导航系统时，你是如何优化导航算法，控制导航耗时在秒级的？具体实现方式是什么
算法：二叉树层序遍历
谈谈你对前端未来发展的看法，可以结合新技术、新趋势等方面进行展开


请简述JSX和Virtual DOM的工作原理，并解释它们在React中的作用

怎么把vue中的template转换成virtual DOM的


阿里云分片上传原理是什么？了解过吗

threejs遇到啥兼容问题没？啥原理了解过吗

错了 浏览器兼容性问题 咋解决的

electron-updater啥原理了解过吗？版本更新不数字签名可以实现更新吗？

jsx和虚拟dom原理说下

app层面怎么做优化（这个题我都没懂）

说说浏览器兼容性问题


Rpc不是很了解

1.崩溃跟踪，日志如何分析
2.无感更新策略3.冷启动白屏优化4.性能优化
5.ronglib、音视频sdk6.常见问题、难点

-----

1.崩溃
工具minidump
存在crash reason 但是比较抽象，文件中有指针信息
原因1: 空指针 读取异常访问冲突 — 截图模块 多个屏幕多个ui进程 拔掉外接屏  数据拿不到
原因2: 内存溢出  — im消息大量diff
原因3: 未知的c++异常
原因4: 异常断点 c++

：node模块： im 截图 Umeet oss
eg： c++调用系统函数 系统版本不同 有的函数没有 导致崩溃

2.无感更新策略
1.找到代码路径，执行文件覆盖策略，mac通，win文件删除存在权限问题
2.electron-updater ：Mac必签名 win可不签名

3.冷启动
1.node模块按需加载 
2.减小包体积 减小壳子的npm依赖
3.减少主进程业务逻辑
4.ipc数据不用对象传输，转为字符串？
5.异步Ipc
6.延迟加载

4.性能优化
1.file协议 本地加载文件
2.减少http请求，减少接口调用
3.优化ipc通信
4.禁用babel编译
5.减少全局变量挂载，内存溢出
6.进程管理 杀死休眠进程

5.音视频sdk
node版本需要与electron的node版本一致

Qiankun缓存了解吗？沙箱底层了解吗？qiankun多页签会有什么问题？怎么通信？内存怎么优化？

Typescript高级接口

Iframe优化


说你们怎么做性能优化的 常用的什么图片压缩 js压缩 webpack分包就别说了 有别的吗 我：没了



###10086、浏览器的工作原理

浏览器的工作原理可以概括为以下几个步骤：

用户输入网址后，浏览器会向服务器发送请求。

服务器接收到请求后，返回HTML文件给浏览器。

浏览器解析HTML文件，构建DOM树、CSSOM树和Render树。DOM树和CSSOM树分别表示HTML文档和CSS样式表的结构和内容，Render树是将DOM树和CSSOM树结合起来，为每个元素计算出最终的样式和布局信息。

浏览器使用Render树渲染页面，同时执行JavaScript脚本，处理用户的交互和动态效果。

当用户在页面上进行交互或发出请求时，浏览器会再次向服务器发送请求，获取新的内容并更新页面。

最后，当用户关闭窗口或者打开新的页面时，浏览器会停止渲染，并释放页面相关的资源。

总体来说，浏览器的工作原理涵盖了从网络请求到页面呈现的整个过程，需要涉及HTML、CSS、JavaScript、HTTP、TCP/IP等多个领域的知识。对于前端开发者而言，了解浏览器的工作原理和机制是非常重要的，有助于优化页面性能、调试和排查问题，提高开发效率和用户体验。

### LRU是什么
LRU 是 Least Recently Used 的缩写，翻译为“最近最少使用”。它是一种缓存淘汰算法，用于在计算机内存（缓存）中管理数据。当缓存达到最大容量时，新的数据需要进入缓存时，需要淘汰缓存中最近最少使用的数据，以腾出空间存储新的数据。这种策略是基于这样一个观察：最近最少使用的数据在未来被访问的概率很小，因此选择淘汰这些数据可以最大程度上利用缓存容量。被淘汰的数据可能被移至磁盘或者被释放空间。


### css scoped原理

### 基本类型

## 源码解析：https://cn.vuejs.org/guide/built-ins/keep-alive.html

## 子组件多个匿名slot 父组件传参时怎么传

### iframe和qiankun啥区别？iframe有没有样式隔离[失望]
### webpack和vite啥区别？
### 不支持esmodule的插件，怎么转换？在哪个阶段转换？
### 签名的实现原理
### http2的原理

### css命名规范

### 从template到dom发生了什么


有些plugin不支持esmodule语法 是在哪个阶段转换的

手写一个plugin的思路？

这几个差价需要学习(了解babel：polyfill、loader、 preset-env及 core之间的关系)[https://zhuanlan.zhihu.com/p/138108118]
(9个常见的 Webpack 面试题，中高级前端必会！)[https://juejin.cn/post/7157998164627161095]


less-loader和scss-loader的区别

webpack做的事情，仅仅是分析出各种模块的依赖关系，然后形成资源列表，最终打包生成到指定的文件中。 更多的功能需要借助loaders和plugins完成。比如说：在代码中有一张图片需要进行打包，webpack可以把图片读出来，但是不能将图片当做js代码来读，所以需要一个加载器loader来帮助我们把图片的二进制数据转变为js代码，loader的作用说白了就是将一种形式的代码通过逻辑转变成另一种形式的代码，转换后的代码webpack就能识别了。loader的功能定位是转换代码

以下就是常见的loader：

image-loader：加载并且压缩图片文件
css-loader：帮助webpack打包处理css文件，使用css-loader必须要配合使用style-loader
style-loader：用于将css编译完成的样式，挂载到页面的style标签上。但是要注意loader执行顺序，style-loader要放在第一位，loader都是从后往前执行
babel-loader：把 ES6 转换成 ES5
sass-loader: css预处理器，能更好的编写css
postcss-loader: 用于补充css样式在各种浏览器的前缀，很方便，不需要手动写了
eslint-loader:用于检查代码是否符合规范，是否存在语法错误
url-loader:处理图片类型资源，可以根据图片的大小进行不同的操作，如果图片大小大于指定大小，则将图片进行资源打包，否则将图片转换为base64格式字符串，再把这个字符串打包到 JS文件里。



webpack开始打包的时候，会加载所有plugin，那么如果plugin不支持es6语法的话，plugin是不是就不会生效？这个兼容问题是怎么解决的？如果你要手写一个plugin，你的思路是什么？


模块化管理是指将代码按照功能或业务划分成一个个独立的模块，每个模块对外暴露出接口和实现细节，方便其他模块调用和使用。在前端开发中，模块化管理通常使用 ES6 模块化规范、CommonJS 规范或 AMD 规范等方式实现。