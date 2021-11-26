---
title: Vue
date: 2021-02-03 10:56:41
tags:
---

## 1. Vuex的action和mutation的特性是什么？区别是什么？Vuex怎么知道state是通过mutation修改还是外部直接修改的？

**Action**
* 一些对State的异步操作可以放在Action中，并通过在Action中的commit Mutation变更状态。
* Action 可以通过store.dispacth()方法触发，或者通过mapActions辅助函数将vue组件的methods方法映射成store.dispacth()调用。

**Mutation**
* 在vuex的严格模式下，Mutation是vuex中改变State的唯一途径
* Mutation中只能同步操作
* 通过store.commit()来调用Mutation

**总结**
mutations可以直接修改state,但只能包含同步操作，同时，只能通过提交commit调用，actions是用来触发mutations的，它无法直接改变state,它可以包含异步操作，但只能通过store.dispacth（）触发。

二、vuex如何知道是如何修改的？

通过$watch监听mutation的commit函数中的_committing是否为true
Vuex中修改state的唯一渠道就是执行commit('xx',payload)方法，其底层通过执行this._withCommit(fn)设置_committing为true,然后才能修改state,修改完毕还需要还原_committing变量。外部修改虽然能直接修改state,但是并没有修改_committing标识位，所以只要watch一下state,state change时判断是否_committing值为tre,即可判断修改的合法性。
