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

