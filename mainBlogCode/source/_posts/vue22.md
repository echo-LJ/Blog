---
title: Vue 的keep-alive
date: 2023-04-19 15:55:31
tags: Vue.js
---

<meta name="referrer" content="no-referrer"/>

### keep-alive是什么？
`keep-alive` 是 Vue 中内置的一个抽象组件,用于`缓存动态组件`或者`路由`,本身不会被渲染,在组件切换时，缓存其包裹的组件的状态，使其不被销毁，防止多次渲染.

### keep-alive的应用场景
用户在某个列表页面选择筛选条件过滤出一份数据列表，由列表页面进入数据详情页面，再返回该列表页面，我们希望：列表页面可以保留用户的筛选（或选中）状态。

### keep-alive的原理
`keep-alive` 是 Vue 中内置的一个抽象组件,用于缓存动态组件或者路由,本身不会被渲染。

**其原理如下：**
1、`keep-alive`本身不会渲染出来，也不会出现在父组件链中,`keep-alive`包裹动态组件或路由。
1、当`动态组件`或`路由`首次渲染时,`keep-alive`会将其VNode缓存起来,并将其从`虚拟 DOM 树`中移除。
2、如果`动态组件`或`路由`被缓存,再次渲染时就不会重新创建和挂载VNode,只需直接从缓存中取出VNode,并将其挂载到对应的位置上即可。
3、当缓存中的`动态组件`或`路由`被离开时,`keep-alive`不会销毁实例,而是将其保存到缓存中。如果缓存中的实例数超过`max`值,会触发`LRU`淘汰策略,将最近未使用的实例销毁。

```
const key = componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')

// componentOptions.Ctor.cid 表示当前组件的唯一标识符，这个值是在组件创建时生成的，用于区分不同的组件类型
// componentOptions.tag 则表示当前组件使用的 HTML 标签名或组件名称。
```

### 首次渲染动态组件或路由时,为什么将其从DOM中移除？

**操作步骤如下：**

1、`keep-alive`监听动态组件或路由的`activated`和`deactivated`生命周期钩子函数。
2、当动态组件或路由被激活时,也就是进入缓存阶段时,`activated`钩子函数会被调用,这是`keep-alive`会将当前的实例的`VNode`存储起来,并将其从父组件的`虚拟DOM树`中移除该节点,并不会销毁其实例。
3、当缓存中的组件或路由被激活时,`activated`钩子函数会被调用,`keep-alive`会从缓存中将当前的实例的`VNode`取出,并将其渲染载对应的位置上。

通过缓存和移除动态组件或者路由实例,keep-alive 可以`减少组件的初始加载时间`和`页面的加载时间`,提高用户体验。

**为什么将其从DOM中移除？**
`keep-alive` 将动态组件或者路由从 DOM 中移除的原因是为了减少不必要的渲染,从而提高页面渲染的性能和流畅度。

在 Vue 中,只要一个组件被渲染,就会生成一棵组件树。每个组件都会对应一颗虚拟 DOM 树,在组件树中包含了该组件的子组件以及所有状态的数据。当组件的状态改变时,组件树会重新构建,所有的组件和子组件都会被重新渲染。

* 不移除: 那么在组件状态发生变化时,VNode会被重新渲染.
* 移除: 只需要重新渲染组件树的一部分,这样就可以降低不必要的渲染,提高性能和流畅度.

### keep-alive的属性

* `include`: 用于指定哪些组件或路由需要缓存。其值为一个字符串或正则表达式,表示匹配到的组件或路由都将被缓存.
```
$ :include="'component1|component2'"
$ :include="/comp.*/"
```
* `exclude`: 用于指定哪些组件或路由不缓存。使用方法与`include`相同。
* `max`: 用于指定最大缓存数。其值为一个数字类型,表示同一时间最多可以缓存的组件/路由实例数,超过最大值,采用LRU淘汰策略,删除实例。
```
$ :max="10"
```

### keep-alive嵌套路由和动态组件的使用方法
Vue3中`keep-alive`结合`vue-router`时变化较大,之前是`keep-alive`包裹`router-view`,现在需要反过来用`router-view`包裹`keep-alive`。

**路由**
```
// Vue3.x
<router-view v-slot="{ Component }">
  <keep-alive>
    <component :is="Component"></component>
  </keep-alive>
</router-view>
// Vue2.x
<keep-alive :include="allowList" :exclude="noAllowList" :max="amount">
    <router-view></router-view>
</keep-alive>
```
**动态组件**

```
<keep-alive :include="allowList" :exclude="noAllowList" :max="amount"> 
    <component :is="currentComponent"></component> 
</keep-alive>
```


### keep-alive缓存后如果要获取数据的方式

**beforeRouteEnter**

在有vue-router的项目,每次进入路由的时候,都会执行`beforeRouteEnter`。

```
beforeRouteEnter(to, from, next){
  next(vm=>{
    console.log(vm)
    // 每次进入路由执行
    vm.getData()  // 获取数据
  })
}
```

**actived**
在keep-alive缓存的组件被激活的时候,都会执行actived钩子
```
activated(){
   this.getData() // 获取数据
}
```

### keep-alive怎么缓存组件的,缓存后又是怎么更新的？

1、结合属性`include`和`exclude`可以明确指定缓存哪些组件或排除缓存指定组件。
2、`keep-alive`是一个通用组件,它的内部定义了一个map,缓存创建过的组件实例,它返回的渲染函数内部会查找内嵌的`component`组件对应组件的   `VNode`,如果该组件在map中找到它就会直接返回,由于`component`的`is属性`是个响应式数据,因此只要它变化,`keep-alive`内部的render函数就会重新执行。





  
### keep-alive包裹的组件是如何使用缓存的

1.  在`首次`加载被包裹组件时,在keep-alive.js中的render函数可知,VNode.componentInstance的值是undfined,keepAlive的值是true,因为keep-alive组件作为父组件,它的render函数会先于被包裹组件执行；由于abstaract为true那么后面的逻辑不执行；
2.  `再次`访问被包裹组件时,VNode.componentInstance的值就是已经缓存的组件实例,那么会执行insert(parentElm, VNode.elm, refElm)逻辑,这样就直接把上一次的DOM插入到父元素中.



### keep-alive的生命周期？

**activated激活**
`activated`: 页面进入的时候会触发

页面第一次进入的时候，钩子函数触发的顺序是: `created->mounted->activated`
当再次前进或者后退的时候只触发:`activated`



**deactivated离开**

`deactivated`: 页面退出的时候会触发


### keep-alive的原理&源码解析
**keep-alive在生命周期钩子函数中操作**

* `created:` 初始化一个`cache`、`keys`，`cache`用来存缓存组件的虚拟DOM集合，`keys`用来存缓存组件的key集合.
* `mounted:`实时监听`include`、`exclude`这两个的变化，并执行相应操作.
* `destroyed:` 删除掉所有缓存相关的东西.

**源码解析**

```
// src/core/components/keep-alive.js

export default {
  name: 'keep-alive',
  abstract: true, // 判断此组件是否需要在渲染成真实DOM
  props: {
    include: patternTypes,
    exclude: patternTypes,
    max: [String, Number]
  },
  created() {
    this.cache = Object.create(null) // 创建对象来存储  缓存虚拟dom
    this.keys = [] // 创建数组来存储  缓存key
  },
  mounted() {
    // 实时监听include、exclude的变动
    this.$watch('include', val => {
      pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
      pruneCache(this, name => !matches(val, name))
    })
  },
  destroyed() {
    for (const key in this.cache) { // 删除所有的缓存
      pruneCacheEntry(this.cache, key, this.keys)
    }
  },
  render() {
      // 下面讲
  }
}
```
**pruneCacheEntry函数**
1、遍历集合，执行所有缓存组件的$destroy方法
2、将cache对应key的内容设置为null
3、删除keys中对应的元素
```
function pruneCacheEntry (
  cache: VNodeCache,
  key: string,
  keys: Array<string>,
  current?: VNode
) {
  const cached = cache[key]
  if (cached && (!current || cached.tag !== current.tag)) {
    cached.componentInstance.$destroy() // 执行组件的destory钩子函数
  }
  cache[key] = null  // 设为null
  remove(keys, key) // 删除对应的元素
}

```

**render函数**

1、获取到keep-alive包裹的第一个组件以及它的组件名称
2、判断此组件名称是否能被白名单、黑名单匹配，如果不能被白名单匹配 || 能被黑名单匹配，则直接返回`VNode`，不往下执行，如果不符合，则往下执行第三步
3、根据组件ID、tag生成缓存key，并在缓存集合中查找是否已缓存过此组件。如果已缓存过，直接取出缓存组件，并更新缓存key在keys中的位置（这是LRU算法的关键），如果没缓存过，则继续第四步
4、分别在cache、keys中保存此组件以及他的缓存key，并检查数量是否超过max，超过则根据LRU算法进行删除。
5、将此组件实例的keepAlive属性`abstract`设置为true.

**keep-alive本身渲染**

Vue在初始化生命周期的时候，为组件实例建立父子关系会根据`abstract`属性决定是否忽略某个组件。在`keep-alive`中，设置了`abstract:true`，那Vue就会跳过该组件实例.
最后构建的组件树中就不会包含`keep-alive`组件，那么由组件树渲染成的DOM树自然也不会有`keep-alive`相关的节点了.

```
// src/core/instance/lifecycle.js

export function initLifecycle (vm: Component) {
  const options = vm.$options
  // 找到第一个非abstract的父组件实例
  let parent = options.parent
  if (parent && !options.abstract) {
    while (parent.$options.abstract && parent.$parent) {
      parent = parent.$parent
    }
    parent.$children.push(vm)
  }
  vm.$parent = parent
  // ...
}
```

## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [靓仔，说一下keep-alive缓存组件后怎么更新及原理？](https://juejin.cn/post/7165675789885636616)
* [keep-alive实现原理](https://zhuanlan.zhihu.com/p/368238830)
* [keep-alive](https://ustbhuangyi.github.io/vue-analysis/v2/extend/keep-alive.html#%E5%86%85%E7%BD%AE%E7%BB%84%E4%BB%B6)