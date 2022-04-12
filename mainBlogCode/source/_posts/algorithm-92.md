---
title: leeCode-92反转链表二(迭代、递归两种解法)
date: 2022-04-12 13:59:56
tags: 算法
---
<meta name="referrer" content="no-referrer"/>


## 题目描述：给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 

**示例1:**

![截屏2022-04-12 下午2.02.08.png](https://upload-images.jianshu.io/upload_images/11846892-19fd5de8d5231ee8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：head = [1,2,3,4,5], left = 2, right = 4
输出：[1,4,3,2,5]
```

**示例2:**
```

输入：head = [5], left = 1, right = 1
输出：[5]

````
## 题解方法一：头插法(非常好理解)

**`算法思路`**

1、我们定义两个指针，分别称之为 g(guard 守卫) 和 p(point)。
我们首先根据方法的参数 m 确定 g 和 p 的位置。将 g 移动到第一个要反转的节点的前面，将 p 移动到第一个要反转的节点的位置上。我们以 m=2，n=4为例。

2、将 p 后面的元素删除，然后添加到 g 的后面。也即头插法。

3、根据 m 和 n 重复步骤（2）

4、返回 dummyHead.next

![1616250561-sZiIjN-img1.png](https://upload-images.jianshu.io/upload_images/11846892-334d93a02b670b07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`代码实现如下`**

```
var reverseBetween = function(head, left, right) {
    // 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
    const dummyNode = new ListNode(-1);
    dummyNode.next = head;

     // 初始化指针
    let g = dummyNode;
    let p = dummyNode.next;
    // 将指针移到相应的位置
    for (let i = 0; i < left - 1; i++) {
        g = g.next;
        p = p.next;
    }

    // 头插法插入节点

    for (let i = 0; i < right - left; i++) {
        let removed = p.next;
        p.next = p.next.next;
        removed.next = g.next;
        g.next = removed;
    }
    return dummyNode.next;
};

```


* 第一步图解：

```
// 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
    const dummyNode = new ListNode(-1);
    dummyNode.next = head;
```
创建了一个节点，它的指针指向head：

`举例解释如下`：dummy_node可以把它理解为一个"哨兵"，因为我们的头结点可能会涉及到反转，当反转之后，原来的头结点就不是返回后的头结点了，举个例子,[5,3]进行1，2的反转，此时head指向的是5节点，而反转之后结果为[3,5]，而此时的head还是指向5，所以我们需要一个哨兵，来坚定不移的站在头结点前，这样不管他如何反转，哨兵dummy_node指向的下一个节点dummy_node.next就是我们要的头结点

![截屏2022-04-12 下午3.02.30.png](https://upload-images.jianshu.io/upload_images/11846892-e1a6db1ed0e604b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





## 题解方法二：迭代（穿针引线）（官方给的绕脑解法）
* 我们以图中黄色区域的链表反转为例。
![[图片上传中...(1615105150-pfWiGq-image.png-faf0cb-1649743802741-0)]
](https://upload-images.jianshu.io/upload_images/11846892-d31b78d04ebfd926.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


使用[206反转链表的迭代解法]，反转left到right部分以后，在拼接起来。我们还需要记录left的前一个节点，和right的后一个节点。如图所示。

![1615105150-pfWiGq-image.png](https://upload-images.jianshu.io/upload_images/11846892-a904b2d4a446e880.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`算法步骤`**
* 1、将待反转的区域反转；
* 2、把pre的next指针指向反转以后的链表头节点，把反转以后的链表的尾节点的next指针指向succ。

![1615105168-ZQRZew-image.png](https://upload-images.jianshu.io/upload_images/11846892-677e3ccdbfa994e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`代码实现如下`**

```
var reverseBetween = function(head, left, right) {
    // 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
    const dummyNode = new ListNode(-1);
    dummyNode.next = head;

    let pre = dummyNode;
    // 第 1 步：从虚拟头节点走 left - 1 步，来到 left 节点的前一个节点
    // 建议写在 for 循环里，语义清晰
    for (let i = 0; i < left - 1; i++) {
        pre = pre.next;
    }

    // 第 2 步：从 pre 再走 right - left + 1 步，来到 right 节点
    let rightNode = pre;
    for (let i = 0; i < right - left + 1; i++) {
        rightNode = rightNode.next;
    }

    // 第 3 步：切断出一个子链表（截取链表）
    let leftNode = pre.next;
    let curr = rightNode.next;

    // 注意：切断链接
    pre.next = null;
    rightNode.next = null;

    // 第 4 步：同第 206 题，反转链表的子区间
    reverseLinkedList(leftNode);

    // 第 5 步：接回到原来的链表中
    pre.next = rightNode;
    leftNode.next = curr;
    return dummyNode.next;
};

const reverseLinkedList = (head) => {
    let pre = null;
    let cur = head;

    while (cur) {
        const next = cur.next;
        cur.next = pre;
        pre = cur;
        cur = next;
    }
}

```
以示例一为例，下面通过图解 来实现 反转链表：

```
head = [1,2,3,4,5], left = 2, right = 4
```
* 第一步图解：

```
// 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
    const dummyNode = new ListNode(-1);
    dummyNode.next = head;
```
创建了一个节点，它的指针指向head：

`举例解释如下`：dummy_node可以把它理解为一个"哨兵"，因为我们的头结点可能会涉及到反转，当反转之后，原来的头结点就不是返回后的头结点了，举个例子,[5,3]进行1，2的反转，此时head指向的是5节点，而反转之后结果为[3,5]，而此时的head还是指向5，所以我们需要一个哨兵，来坚定不移的站在头结点前，这样不管他如何反转，哨兵dummy_node指向的下一个节点dummy_node.next就是我们要的头结点

![截屏2022-04-12 下午3.02.30.png](https://upload-images.jianshu.io/upload_images/11846892-e1a6db1ed0e604b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第二步图解：

```

    let pre = dummyNode;
    // 第 1 步：从虚拟头节点走 left - 1 步，来到 left 节点的前一个节点
    // 建议写在 for 循环里，语义清晰
    for (let i = 0; i < left - 1; i++) {
        pre = pre.next;
    }
```

获取需要反转的子链表（黄色）的前一个节点

![截屏2022-04-12 下午2.55.01.png](https://upload-images.jianshu.io/upload_images/11846892-67af6f4cd2ea8fdc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
// i = 0; 执行第一次for循环

pre = pre.next;
```
![截屏2022-04-12 下午2.57.14.png](https://upload-images.jianshu.io/upload_images/11846892-0a4dcb89f3b042ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第三步图解：
```
// 第 2 步：从 pre 再走 right - left + 1 步，来到 right 节点
    let rightNode = pre;
    for (let i = 0; i < right - left + 1; i++) {
        rightNode = rightNode.next;
    }

```
获取需要反转的子链表（黄色）的尾部节点
```
let rightNode = pre;
```
![截屏2022-04-12 下午3.11.53.png](https://upload-images.jianshu.io/upload_images/11846892-99cda9ca5e98ccca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
// i = 0; i< 3 ;执行第一次for循环

rightNode = rightNode.next;
```
![截屏2022-04-12 下午3.15.09.png](https://upload-images.jianshu.io/upload_images/11846892-429d14c4e9f81149.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
// i = 1; i< 3 ;执行第二次for循环

rightNode = rightNode.next;
```
![截屏2022-04-12 下午3.15.43.png](https://upload-images.jianshu.io/upload_images/11846892-0b4564d921a6f924.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
// i = 2; i< 3 ;执行第三次for循环

rightNode = rightNode.next;
```

![截屏2022-04-12 下午3.16.37.png](https://upload-images.jianshu.io/upload_images/11846892-911b2ad32fa512f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第四步图解：
```
// 第 3 步：切断出一个子链表（截取链表）
    let leftNode = pre.next;
    let curr = rightNode.next;

    // 注意：切断链接
    pre.next = null;
    rightNode.next = null;
```

获取需要反转的链表的头部
```
let leftNode = pre.next;
```
![截屏2022-04-12 下午3.04.03.png](https://upload-images.jianshu.io/upload_images/11846892-059bfab976beec77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

获取需要反转的链表的尾部的下一个节点：curr;反转后的链表的尾部的next指向curr;
```
let curr = rightNode.next;
```
![截屏2022-04-12 下午3.24.07.png](https://upload-images.jianshu.io/upload_images/11846892-c9f080900a34968c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

// 切断需要反转的子链表的前后链接
```
    // 注意：切断链接
    pre.next = null;
    rightNode.next = null;
```
![截屏2022-04-12 下午3.25.42.png](https://upload-images.jianshu.io/upload_images/11846892-dc66cd58529231db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第五步图解：
```
    // 第 4 步：同第 206 题，反转链表的子区间
    reverseLinkedList(leftNode);

```
请参考：缺地址
![截屏2022-04-12 下午3.28.52.png](https://upload-images.jianshu.io/upload_images/11846892-da5acd373fe15f8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 第六步图解：
```
    // 第 5 步：接回到原来的链表中
    pre.next = rightNode;
    leftNode.next = curr;

```
![截屏2022-04-12 下午3.29.33.png](https://upload-images.jianshu.io/upload_images/11846892-de3c634e69d56881.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

整个图解完成！！

疑惑？？？：下面这个reverseLinkedList不是一个fun么，为什么没有return, 就可以改变leftNode 呢？
答疑：
```
//只是改变了指针的方向， left永远指向1 ，left.next 指向null
 (left)1->2->3->4(right)
(left)1<-2<-3<-4(right)
leftnode一直指向1
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

