---
title: 如何排查「内存泄漏」导致的页面卡顿现象
date: 2023-12-05 13:41:34
tags: web
---

<meta name="referrer" content="no-referrer"/>

## 前言？

**`如果页面卡顿，你觉得可能是什么原因造成的？有什么办法锁定原因并解决吗？`**
这是一个非常宽泛而又有深度的问题，他涉及到很多的页面性能优化问题

* 先检查是否是网络请求太多，导致数据返回过慢，适当做一些缓存。
* 排查是否是某块资源的bundle太大，考虑拆分一下
* 排查JS代码，是不是有过多循环导致占用主线程时间过长。
* 浏览器某帧渲染过多，导致卡帧。
* 排查是否在页面渲染过程中，导致过多的重排重绘。
* 排查是否是内存泄露。

### 内存泄露是什么？

`内存泄漏就是指由于疏忽或者程序的某些错误造成未能释放已经不再使用的内存的情况。

`举个🌰`：假设某个变量占用100M的内存，而你又用不到这个变量，但是这个变量没有被手动的回收或自动回收，即仍然占用100M的内存空间，这就是一种内存的浪费，即内存泄漏


### 👋 Chrome devTools查看内存情况

`如何使用Chrome的开发者工具来查看js内存情况`

* 首先打开Chrome的无痕模式:无痕模式可以屏蔽掉Chrome插件对我们之后测试内存占用情况的影响.
* 然后打开开发者工具，找到`Performance`这一栏，可以看到其内部带着一些功能按钮：开始录制按钮、刷新页面按钮、清空记录按钮、记录并可视化js内存、节点、事件监听器按钮、触发垃圾回收机制按钮等等。

![img1.png](https://upload-images.jianshu.io/upload_images/11846892-a3583b5743dafa49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![img2.png](https://upload-images.jianshu.io/upload_images/11846892-910314e7ba9456ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


从上图中我们可以看到，在页面从零到加载完成这个过程中`JS Heap（js堆内存）`、`documents（文档）`、`Nodes（DOM节点）`、`Listeners（监听器）`、`GPU memory（GPU内存`）的最低值、最高值以及随时间的走势曲线，这也是我们主要关注的点

* 看看开发者工具中的`Memory`一栏: 主要是用于记录页面`堆内存的具体情况`以及`js堆内存随加载时间线动态的分配情况`.

![img3.png](https://upload-images.jianshu.io/upload_images/11846892-01d84d68c07a3992.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

堆快照就像照相机一样，能记录你当前页面的堆内存情况，每快照一次就会产生一条快照记录，如图所示：

![img4.png](https://upload-images.jianshu.io/upload_images/11846892-72d69b2784a1ea8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图所示，刚开始执行了一次快照，记录了当时堆内存空间占用为13.9MB，然后我们点击了页面中某些按钮，又执行一次快照，记录了当时堆内存空间占用为13.4MB。并且点击对应的快照记录，能看到当时所有内存中的变量情况（结构、占总占用内存的百分比...）

然后我们还可以看一下页面动态的内存变化情况，如图所示：

![img5.png](https://upload-images.jianshu.io/upload_images/11846892-ff205844c7d98e48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在开始记录后，我们可以看到图中右上角有起伏的蓝色与灰色的柱形图，其中蓝色表示当前时间线下占用着的内存；灰色表示之前占用的内存空间已被清除释放。

从上图过程来看，我们可以看到刚开始处于的tab所对应显示的页面中占用了一定的堆内存空间，成蓝色柱形，在点击别的tab后，原tab对应的内容消失，并且原来蓝色的柱形变成灰色（表示原占用的内存空间得到了释放），同时新tab所对应显示的页面也占用了一定的堆内存空间。因此后续我们就可以针对这个图来查看内存的占用与清除情况

### 🔥 内存泄漏的场景
`那么到底有哪些情况会出现内存泄漏的情况呢？这里列举了常见的几种：`

* 闭包使用不当引起内存泄漏
* 全局变量
* 分离的DOM节点
* 控制台的打印
* 遗忘的定时器

**闭包使用不当**
```
function fn1 () {
    let a = {
        name: '零一'
    }

    let b = 3

    function fn2() {
        let c = [1, 2, 3]
    }

    fn2()

    return a
}

let res = fn1()
```
在上面这个例子中，在退出fn1函数执行上下文后，该上下文中的变量a本应被当作垃圾数据给回收掉，但因fn1函数最终将变量a返回并赋值给全局变量res，其产生了对变量a的引用，所以变量a被标记为活动变量并一直占用着相应的内存，假设变量res后续用不到，这就算是一种闭包使用不当的例子。


接下来尝试使用`Performance`和`Memory`来查看一下闭包导致的内存泄漏问题，为了使内存泄漏的结果更加明显，我们稍微改动一下文章开头的例子，代码如下：

```
<button onclick="myClick()">执行fn1函数</button>
<script>
    function fn1 () {
        let a = new Array(10000)  // 这里设置了一个很大的数组对象

        let b = 3

        function fn2() {
            let c = [1, 2, 3]
        }

        fn2()

        return a
    }

    let res = []  

    function myClick() {
        res.push(fn1())
    }
</script>
```

设置了一个按钮，每次执行就会将fn1函数的返回值添加到全局数组变量res中，是为了能在performacne的曲线图中看出效果，如图所示：

![img1.png](https://upload-images.jianshu.io/upload_images/11846892-5eebc5d90482b768.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在每次录制开始时手动触发一次垃圾回收机制，这是为了确认一个初始的堆内存基准线，便于后面的对比，然后我们点击了几次按钮，即往全局数组变量res中添加了几个比较大的数组对象，最后再触发一次垃圾回收，发现录制结果的JS Heap曲线刚开始成阶梯式上升的，最后的曲线的高度比基准线要高，说明可能是存在内存泄漏的问题


在得知有内存泄漏的情况存在时，我们可以改用Memory来更明确得确认问题和定位问题:

首先可以用`Allocation instrumentation on timeline`来确认问题，如下图所示：

![img2.png](https://upload-images.jianshu.io/upload_images/11846892-c82756830588827d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在我们每次点击按钮后，动态内存分配情况图上都会出现一个蓝色的柱形，并且在我们触发垃圾回收后，蓝色柱形都没变成灰色柱形，即之前分配的内存并未被清除

所以此时我们就可以更明确得确认内存泄漏的问题是存在的了，接下来就精准定位问题，可以利用Heap snapshot来定位问题，如图所示：

![gif1.gif](https://upload-images.jianshu.io/upload_images/11846892-78cc366e786de34e.gif?imageMogr2/auto-orient/strip)


第一次先点击快照记录初始的内存情况，然后我们多次点击按钮后再次点击快照，记录此时的内存情况，发现从原来的1.1M内存空间变成了1.4M内存空间，然后我们选中第二条快照记录，可以看到右上角有个All objects的字段，其表示展示的是当前选中的快照记录所有对象的分配情况，而我们想要知道的是第二条快照与第一条快照的区别在哪，所以选择Object allocated between Snapshot1 and Snapshot2，即展示第一条快照和第二条快照存在差异的内存对象分配情况，此时可以看到Array的百分比很高，初步可以判断是该变量存在问题，点击查看详情后就能查看到该变量对应的具体数据了。

以上就是一个判断闭包带来内存泄漏问题并简单定位的方法了

**全局变量**

全局的变量一般是不会被垃圾回收掉的，在文章开头也提到过了。当然这并不是说变量都不能存在全局，只是有时候会因为疏忽而导致某些变量流失到全局，例如未声明变量，却直接对某变量进行赋值，就会导致该变量在全局创建，如下所示：

```
function fn1() {
    // 此处变量name未被声明
    name = new Array(99999999)
}

fn1()
```
此时这种情况就会在全局自动创建一个变量name，并将一个很大的数组赋值给name，又因为是全局变量，所以该内存空间就一直不会被释放.

**3.分离的DOM节点**

什么叫DOM节点？假设你手动移除了某个dom节点，本应释放该dom节点所占用的内存，但却因为疏忽导致某处代码仍对该被移除节点有引用，最终导致该节点所占内存无法被释放，例如这种情况：

```
<div id="root">
    <div class="child">我是子元素</div>
    <button>移除</button>
</div>
<script>

    let btn = document.querySelector('button')
    let child = document.querySelector('.child')
    let root = document.querySelector('#root')
    
    btn.addEventListener('click', function() {
        root.removeChild(child)
    })

</script>
```


该代码所做的操作就是点击按钮后移除.child的节点，虽然点击后，该节点确实从dom被移除了，但全局变量child仍对该节点有引用，所以导致该节点的内存一直无法被释放,可以尝试用Memory的快照功能来检测一下，如图所示：

![png1.png](https://upload-images.jianshu.io/upload_images/11846892-a3e01bb3b5b78c09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


同样的先记录一下初始状态的快照，然后点击移除按钮后，再点击一次快照，此时内存大小我们看不出什么变化，因为移除的节点占用的内存实在太小了可以忽略不计，但我们可以点击第二条快照记录，在筛选框里输入detached，于是就会展示所有脱离了却又未被清除的节点对象

解决办法如下图所示：
```
<div id="root">
    <div class="child">我是子元素</div>
    <button>移除</button>
</div>
<script>
    let btn = document.querySelector('button')

    btn.addEventListener('click', function() {  
        let child = document.querySelector('.child')
        let root = document.querySelector('#root')

        root.removeChild(child)
    })

</script>

```

**控制台的打印**

控制台的打印也会造成内存泄漏吗？？？？是的呀，如果浏览器不一直保存着我们打印对象的信息，我们为何能在每次打开控制的Console时看到具体的数据呢？先来看一段测试代码：

```
<button>按钮</button>
<script>
    document.querySelector('button').addEventListener('click', function() {
        let obj = new Array(1000000)

        console.log(obj);
    })
</script>

```

我们在按钮的点击回调事件中创建了一个很大的数组对象并打印，用performance来验证一下：

![png3.png](https://upload-images.jianshu.io/upload_images/11846892-93b96e2b5c05dfc0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开始录制，先触发一次垃圾回收清除初始的内存，然后点击三次按钮，即执行了三次点击事件，最后再触发一次垃圾回收。查看录制结果发现JS Heap曲线成阶梯上升，并且最终保持的高度比初始基准线高很多，这说明每次执行点击事件创建的很大的数组对象obj都因为console.log被浏览器保存了下来并且无法被回收

接下来注释掉 console.log,再来看一下结果：

![img4.png](https://upload-images.jianshu.io/upload_images/11846892-aead612a6c831eae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后简单总结一下：在开发环境下，可以使用控制台打印便于调试，但是在生产环境下，尽可能得不要在控制台打印数据。样就避免了生产环境下无用的变量打印占用一定的内存空间，同样的除了console.log之外，console.error、console.info、console.dir等等都不要在生产环境下使用.

**遗忘的定时器**

其实定时器也是平时很多人会忽略的一个问题，比如定义了定时器后就再也不去考虑清除定时器了，这样其实也会造成一定的内存泄漏.

```
<button>开启定时器</button>
<script>

    function fn1() {
        let largeObj = new Array(100000)
        setInterval(() => {
            let myObj = largeObj
        }, 1000)
    }

    document.querySelector('button').addEventListener('click', function() {
        fn1()
    })
</script>
```
这段代码是在点击按钮后执行fn1函数，fn1函数内创建了一个很大的数组对象largeObj，同时创建了一个setInterval定时器，定时器的回调函数只是简单的引用了一下变量largeObj，我们来看看其整体的内存分配情况吧：


![截屏2023-12-05 下午3.38.48.png](https://upload-images.jianshu.io/upload_images/11846892-221be2775afd5b29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 **总结：** 用到了定时器，如果在用不到定时器后一定要清除掉，否则就会出现本例中的情况。除了setTimeout和setInterval，其实浏览器还提供了一个API也可能就存在这样的问题，那就是requestAnimationFrame

 **实用场景：**Vue 项目，很多组件 mounted 绑定自定义事件（eventBus，on off emit once 等 API），但unmount 时没有 off 。组件因为业务功能，会频繁的创建和销毁，所以就导致了内存泄漏。
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️



参考链接:
* [一文带你了解如何排查内存泄漏导致的页面卡顿现象](https://juejin.cn/post/6947841638118998029)

