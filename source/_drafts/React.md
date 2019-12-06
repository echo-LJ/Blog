---
title: React
tags:
---

### 组件的生命周期


#### 组件在初始化时会触发5个钩子函数：
###### 1、getDefaultProps()
设置默认的props，也可以用dufaultProps设置组件的默认属性
###### 2、getInitialState()
在使用es6的class语法时是没有这个钩子函数的，可以直接在constructor中定义this.state。此时可以访问this.props。
###### 3、componentWillMount()
组件初始化时只调用，以后组件更新不调用，整个生命周期只调用一次，此时可以修改state。
###### 4、render()
react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行。此时就不能更改state了。
###### 5、componentDidMount()
组件渲染之后调用，可以通过this.getDOMNode()获取和操作dom节点，只调用一次。

#### 组件在初始化时会触发5个钩子函数：

###### 1、componentWillReceivePorps(nextProps)
组件初始化时不调用，组件接受新的props时调用。
###### 2、shouldComponentUpdate(nextProps, nextState)
react性能优化非常重要的一环。组件接受新的state或者props时调用，我们可以设置在此对比前后两个`props`和`state`是否相同，如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，节省大量性能，尤其是在dom结构复杂的时候。不过调用`this.forceUpdate`会跳过此步骤。
###### 3、componentWillUpdate(nextProps, nextState)
组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state
！！修改state不能用this.setState(),该方法会触发另一个componentWillUpdate，会陷入死循环。
（github issue查询建议使用nextState.test = 1这种写法，没有验证）
###### 4、render()
###### 5、componentDidUpdate()
组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点。
###### 6、componentWillUnmount()
组件将要卸载时调用，一些事件监听和定时器需要在此时清除。
### React-Router路由
### 组件之间的通信


* 父子之间通信
父子级关系，父级可以将一个`回调函数`当作`属性`传递给子级，子级可以直接调用函数从而和父级通信。
* 组件层级嵌套到比较深
使用上下文`getChildContext`来传递信息，这样在不需要将函数一层层往下传，任何一层的子级都可以通过`this.context`直接访问。
* 兄弟关系的组件之间
1. 只能利用同一层的上级作为中转站.
2. 兄弟组件都是最高层的组件, 利用redux

* 组件之间的信息还可以通过全局事件来传递.
不同页面可以通过参数传递数据，下个页面可以用location.param来获取。其实react本身很简单，难的在于如何优雅高效的实现组件之间数据的交流。

### Redux
* 作用

进行逻辑运算、储存数据和实现组件尤其是顶层组件的通信

* 实现逻辑

react-redux提供了connect和Provider两个好基友，它们一个将组件与redux关联起来，一个将store传给组件。组件通过dispatch发出action，store根据action的type属性调用对应的reducer并传入state和这个action，reducer对state进行处理并返回一个新的state放入store，connect监听到store发生变化，调用setState更新组件，此时组件的props也就跟着变化。

值得注意的是`connect，Provider，mapStateToProps,mapDispatchToProps`是react-redux提供的，redux本身和react没有半毛钱关系，它只是数据处理中心，没有和react产生任何耦合，是react-redux让它们联系在一起

* redux主要由三部分组成：store，reducer，action

#### store是一个对象，它有四个主要的方法：
###### 1、dispatch:

用于action的分发——在createStore中可以用middleware中间件对dispatch进行改造，比如当action传入dispatch会立即触发reducer，有些时候我们不希望它立即触发，而是等待异步操作完成之后再触发，这时候用redux-thunk对dispatch进行改造，以前只能传入一个对象，改造完成后可以传入一个函数，在这个函数里我们手动dispatch一个action对象，这个过程是可控的，就实现了异步。
###### 2、subscribe：
监听state的变化——这个函数在store调用dispatch时会注册一个listener监听state变化，当我们需要知道state是否变化时可以调用，它返回一个函数，调用这个返回的函数可以注销监听。 
```
let unsubscribe = store.subscribe(() => {console.log('state发生了变化')})
```

###### 3、getState：
获取store中的state——当我们用action触发reducer改变了state时，需要再拿到新的state里的数据，毕竟数据才是我们想要的。
getState主要在两个地方需要用到:
* 一是在dispatch拿到action后store需要用它来获取state里的数据，并把这个数据传给reducer，这个过程是自动执行的.

* 二是在我们利用subscribe监听到state发生变化后调用它来获取新的state数据，如果做到这一步，说明我们已经成功了。
###### 4、replaceReducer:
替换reducer，改变state修改的逻辑。

store可以通过`createStore()`方法创建，接受三个参数，经过combineReducers合并的reducer和state的初始状态以及改变dispatch的中间件，后两个参数并不是必须的。store的主要作用是将action和reducer联系起来并改变state。

#### action:

action是一个对象，其中type属性是必须的，同时可以传入一些数据。action可以用actionCreactor进行创造。dispatch就是把action对象发送出去。

#### reducer:

reducer是一个函数，它接受一个`state`和一个`action`，根据action的type返回一个新的state。根据业务逻辑可以分为很多个reducer，然后通过combineReducers将它们合并，state树中有很多对象，每个state对象对应一个reducer，state对象的名字可以在合并时定义。

```
const reducer = combineReducers({
     a: doSomethingWithA,
     b: processB,
     c: c
})
```
#### combineReducers:

其实它也是一个reducer，它接受整个state和一个action，然后将整个state拆分发送给对应的reducer进行处理，所有的reducer会收到相同的action，不过它们会根据action的type进行判断，有这个type就进行处理然后返回新的state，没有就返回默认值，然后这些分散的state又会整合在一起返回一个新的state树。

> 接下来分析一下整体的流程，首先调用store.dispatch将action作为参数传入，同时用getState获取当前的状态树state并注册subscribe的listener监听state变化，再调用combineReducers并将获取的state和action传入。combineReducers会将传入的state和action传给所有reducer，并根据action的type返回新的state，触发state树的更新，我们调用subscribe监听到state发生变化后用getState获取新的state数据。

> redux的state和react的state两者完全没有关系，除了名字一样。

* `使用流程`

如果只使用redux，那么流程是这样的：

component --> dispatch(action) --> reducer --> subscribe --> getState --> component

用了react-redux之后流程是这样的：

component --> actionCreator(data) --> reducer --> component

store的三大功能：dispatch，subscribe，getState都不需要手动来写了。react-redux帮我们做了这些，同时它提供了两个好基友Provider和connect。


参考链接：https://github.com/bailicangdu/react-pxq

# React

#### 1. 什么时候使用状态管理器？
参考链接：https://baijiahao.baidu.com/s?id=1590300061305698080&wfr=spider&for=pc

#### 2. render函数中return如果没有使用()会有什么问题？
在使用JSX语法书写react代码时，babel会将JSX语法编译成js，同时会在每行自动添加分号（；），如果return后换行了，那么就会变成 return； 一般情况下会报错：
* Nothing was returned from render. This usually means a return statement is missing. Or, to render nothing, return null.
解决办法：为了代码可读性我们一般会在return后面添加括号这样代码可以折行书写，否则就在return 后面紧跟着语句，这样也是可以的。

#### 3. componentWillUpdate可以直接修改state的值吗？
`本题参考上面的生命周期函数componentWillUpdate的解释`
react 组件在每次需要重新渲染时候都会调用 componentWillUpdate(),

例如，我们调用 this.setState()时候

在这个函数中我们之所以不调用 this.setState()是因为该方法会触发另一个 componentWillUpdate(),如果我们 componentWillUpdate()中触发状态更改,我们将以无限循环.

！！（github issue查询建议使用nextState.test = 1这种写法，没有验证）

#### 4. 什么渲染劫持？
什么是渲染劫持：渲染劫持的概念是控制组件从另一个组件输出的能力，当然这个概念一般和react中的高阶组件（HOC）放在一起解释比较有明了。

高阶组件可以在render函数中做非常多的操作，从而控制原组件的渲染输出，只要改变了原组件的渲染，我们都将它称之为一种渲染劫持。

实际上，在高阶组件中，组合渲染和条件渲染都是渲染劫持的一种，通过反向继承，不仅可以实现以上两点，还可以增强由原组件render函数产生的React元素。

实际的操作中 通过 操作 state、props 都可以实现渲染劫


#### 4. 对 React Context 的理解以及应用？
在React的官方文档中，Context被归类为高级部分(Advanced)，属于React的高级API，但官方并不建议在稳定版的App中使用Context。
使用例子：比如react-redux的<Provider />，就是通过Context提供一个全局态的store，拖拽组件react-dnd，通过Context在组件中分发DOM的Drag和Drop事件，路由组件react-router通过Context管理路由状态
* 使用Context，可以跨越组件进行数据传递。
* 如何使用Context
参考链接：https://www.jianshu.com/p/eba2b76b290b

#### 5. 为什么 React 并不推荐我们优先考虑使用 Context？

Context 目前还处于实验阶段，可能会在后面的发行版本中有很大的变化，事实上这种情况已经发生了，所以为了避免给今后升级带来大的影响和麻烦，不建议在 app 中使用 context。
尽管不建议在 app 中使用 context，但是独有组件而言，由于影响范围小于 app，如果可以做到高内聚，不破坏组件树之间的依赖关系，可以考虑使用 context
对于组件之间的数据通信或者状态管理，有效使用 props 或者 state 解决，然后再考虑使用第三方的成熟库进行解决，以上的方法都不是最佳的方案的时候，在考虑 context。

context 的更新需要通过 setState()触发，但是这并不是很可靠的，Context 支持跨组件的访问，但是如果中间的子组件通过一些方法不影响更新，比如 shouldComponentUpdate() 返回 false 那么不能保证 Context 的更新一定可以使用 Context 的子组件，因此，Context 的可靠性需要关注。

#### 6. 说说你对windowing的了解-react-window和react-virtualized？

展示长列表
如果你的应用会渲染大量的列表数据，我们建议使用一种称为‘windowing’的技术，这种技术下在任何给定的时间内只会渲染一小部分数据列表，并可以减少列表项的重复渲染（即再次渲染已经渲染过的数据）。

`react-window`和`react-virtualized`都是流行的使用windowing技术的库，他们都提供了一系列可重用的组件，这些组件能够帮助你以最好的性能展示列表以及表格数据.

#### 7. 举例说明 React 的插槽有哪些运用场景？
对于 portal 的一个典型用例是当父组件有 overflow: hidden 或 z-index 样式，但你需要子组件能够在视觉上 “跳出(break out)” 其容器。例如，对话框、hovercards 以及提示框。所以一般 react 组件里的模态框，就是这样实现的

#### 8. 你有用过 React 的插槽(Portals)吗？怎么用？
* 首先简单的介绍下 react 中的插槽（Portals），通过 ReactDOM.createPortal(child, container)创建，是 ReactDOM 提供的接口，可以实现将子节点渲染到父组件 DOM 层次结构之外的 DOM 节点。
* 第一个参数（child）是任何可渲染的 React 子元素，例如一个元素，字符串或 片段(fragment)。第二个参数（container）则是一个 DOM 元素。

* 对于 portal 的一个典型用例是当父组件有 overflow: hidden 或 z-index 样式，但你需要子组件能够在视觉上 “跳出(break out)” 其容器。例如，对话框、hovercards 以及提示框。所以一般 react 组件里的模态框，就是这样实现的。
#### 9. React 的严格模式有什么用处？

react 的 strictMode 是一个突出显示应用程序中潜在问题的工具，与 Fragment 一样，strictMode 不会渲染任何的可见 UI，它为其后代元素触发额外的检查和警告。

注意：严格模式仅在开发模式下运行，它们不会影响生产构建

可以为程序的任何部分使用严格模式

```
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```
在上述的示例中，不会对 Header 和 Footer 组件运行严格模式检查。但是，ComponentOne 和 ComponentTwo 以及它们的所有后代元素都将进行检查。

StrictMode 目前有助于：

* 识别不安全的生命周期
* 关于使用过时字符串 ref API 的警告
* 关于使用废弃的 findDOMNode 方法的警告
* 检测意外的副作用
* 检测过时的 context API

#### 10.React如何进行代码拆分？拆分的原则是什么？

个人认为react的拆分前提是代码目录设计规范，模块定义规范，代码设计规范，符合程序设计的一般原则，例如高内聚、低耦合等等。

在react项目中：
1. 在api层面单独封装，对外暴露http请求的结果。
2. 数据层面使用react-redux， 异步中间件使用的是react-thunk封装处理异步请求，和业务层逻辑处理。
3. 视图层，尽量使用redux层传过来的数据，修改逻辑，也是重新触发action更改props.
4. 静态类型的资源单独放置。
5. 公共组件、高阶组件、插件单独放置。
6. 工具类文件单独放置。

#### 10.React组件的构造函数有什么作用？

在react的新的写法中，每一个组件都是一个类，这个很符合es6的语法规范，在es6中想要创建一个对象，就要调用相应的构造函数，react的组件渲染有两种情况，第一种情况是第一次渲染，第二种情况是状态更新时候重新渲染构造函数，在组件的初次渲染时只会运行一次。

构造函数里面进行的操作一般会有三种用途：

* 1. 指定this -->super(props)
* 2. 设置初始化的状态 --> this.setState({});
* 3. 为组件上的构造函数绑定this

#### 11.为什么说react中的props是只读的？

react官方文档中说到：组件无论是使用函数声明还是通过class声明，都不能修改自身的props,props作为组件对外通信的一个接口，为了保证组件像纯函数一样没有响应的副作用，所有的组件都必须像纯函数一样保护他们的props不被修改。

#### 12.`super()`和`super(props)`有什么区别？

react中的class是基于es6的规范实现的，继承是使用extends关键字实现继承的，字类必须在constructor()中调用super()方法否则新建实力就会报错；
报错的原因就是：子类是没有自己的this对象的，它只能继承父类的this对象，然后对其进行加工，而super()就是将父类中的this对象继承给子类的，没有super()子类就得不到this对象。
如果你使用了constructor就必须使用super()，这个是用来初始化this的，可以绑定事件到this上，如果想要在constructor中使用this.props,就必须给super添加参数super(props)；
注意：无论有没有constructor，在render中的this.props都是可以使用的，这是react自动附带的，如果没有用到constructor是可以不写的，react会默认添加一个空的constroctor.


#### 13.怎么动态到导入组件？

* 使用import和async/await实现的异步组件
* react.lazy
* 开源库react-loadable/react-lazyload库
* babel动态导入































#### 说说你对React的渲染原理的理解？
持

#### 4. React Intl是什么原理？
react-intl 实现多语言项目国际化处理




