---
title: leeCode-25： K 个一组翻转链表： (迭代解法)-困难
date: 2022-04-13 10:53:05
tags: 算法
---
<meta name="referrer" content="no-referrer"/>


## 题目描述：给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

**示例1:**

![截屏2022-04-12 下午2.02.08.png](https://upload-images.jianshu.io/upload_images/11846892-19fd5de8d5231ee8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：head = [1,2,3,4,5], k = 2
输出：[2,1,4,3,5]
```
![截屏2022-04-13 上午10.55.47.png](https://upload-images.jianshu.io/upload_images/11846892-48541437706e6b3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**示例2:**
```

输入：head = [1,2,3,4,5], k = 3
输出：[3,2,1,4,5]

```
![截屏2022-04-13 上午10.55.57.png](https://upload-images.jianshu.io/upload_images/11846892-e93cb94c6c44c346.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**示例3:**
```

输入：head = [1,2,3,4,5], k = 1
输出：[1,2,3,4,5]

```

**示例4:**
```

输入：head = [1], k = 1
输出：[1]

```


## 题解方法一：迭代法(非常好理解)

**`算法思路`**

1、链表分区为已翻转部分+待翻转部分+未翻转部分
2、每次翻转前，要确定翻转链表的范围，这个必须通过 k 此循环来确定
3、需记录翻转链表前驱和后继，方便翻转完成后把已翻转部分和未翻转部分连接起来
4、初始需要两个变量 pre 和 end，pre 代表待翻转链表的前驱，end 代表待翻转链表的末尾
5、经过k此循环，end 到达末尾，记录待翻转链表的后继 next = end.next
6、翻转链表，然后将三部分链表连接起来，然后重置 pre 和 end 指针，然后进入下一次循环
7、特殊情况，当翻转部分长度不足 k 时，在定位 end 完成后，end==null，已经到达末尾，说明题目已完成，直接返回即可
8、时间复杂度为 O(n*K)O(n∗K) 最好的情况为 O(n)O(n) 最差的情况未 O(n^2)O(n^2)
9、空间复杂度为 O(1)O(1) 除了几个必须的节点指针外，我们并没有占用其他空间

![866b404c6b0b52fa02385e301ee907fc015742c3766c80c02e24ef3a8613e5ad-k个一组翻转链表.png](https://upload-images.jianshu.io/upload_images/11846892-4b10542bad671b1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`代码实现如下`**

```
var reverseKGroup = function(head, k) {
    // 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
    const dummyNode = new ListNode(-1);
    dummyNode.next = head;

    let pre = dummyNode;
    let end = dummyNode;
    
    while(end.next !=null) {
        for(let i = 0;i < k & end !==null; i++) end = end.next
        if (end == null) break;
        let start = pre.next;
        let next = end.next;
        end.next = null;
        pre.next = null;
        pre.next = reverseListNode(start)
        start.next = next;
        pre = start
        end = pre;

    }
    return dummyNode.next;
};

var reverseListNode = (head) => {
    let prev = null;
    let curr = head;
    while(curr){
        let next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    return prev
}


```

以下面的链表为例，下面通过图解 来实现 反转链表：

```
head = [1,2,3,4,5,6,7,8], k = 3

输入：head = [1,2,3,4,5,6,7,8], k = 3
输出：[3,2,1,6,5,4,7,8]
```

* 第一步图解：

```
// 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
    const dummyNode = new ListNode(-1);
    dummyNode.next = head;
```
创建了一个节点，它的指针指向head：

`举例解释如下`：dummy_node可以把它理解为一个"哨兵"，因为我们的头结点可能会涉及到反转，当反转之后，原来的头结点就不是返回后的头结点了，举个例子,[5,3]进行1，2的反转，此时head指向的是5节点，而反转之后结果为[3,5]，而此时的head还是指向5，所以我们需要一个哨兵，来坚定不移的站在头结点前，这样不管他如何反转，哨兵dummy_node指向的下一个节点dummy_node.next就是我们要的头结点

![截屏2022-04-13 下午2.30.19.png](https://upload-images.jianshu.io/upload_images/11846892-0d89ead96b71a952.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* 第二步图解：

```
 // 初始化 pre：翻转链表的前驱、end翻转链表的末尾
    let pre = dummyNode;
    let end = dummyNode;
```


![截屏2022-04-13 下午3.17.11.png](https://upload-images.jianshu.io/upload_images/11846892-d904c8f1511aa480.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来进入for循环，逐行进行分析；
* 第三步图解：

```
while(end.next !=null) { // 举例： 如果链表为[1] k = 1; 则跳出for循环直接输出即可;
```
* 第四步图解：
```
    for(let i = 0;i < k & end !==null; i++) end = end.next
    if (end == null) break;
```
获取需要翻转的链表的尾部：end
![截屏2022-04-13 下午3.21.30.png](https://upload-images.jianshu.io/upload_images/11846892-f2349e2a3319eaab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 第五步图解：
```
    let start = pre.next;
    let next = end.next;
```
获取需要翻转的链表的头部：start，获取待翻转链表的后继 next 
![截屏2022-04-13 下午3.23.02.png](https://upload-images.jianshu.io/upload_images/11846892-52dff90bbd89d79e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第六步图解：
```
end.next = null;
pre.next = null;
```
断开所有链接：
![截屏2022-04-13 下午3.25.37.png](https://upload-images.jianshu.io/upload_images/11846892-91345ff5cdd1ebd4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第八步图解：
```
 pre.next = reverseListNode(start)
        start.next = next;
```
翻转之后，重新建立连接：
![截屏2022-04-13 下午3.27.38.png](https://upload-images.jianshu.io/upload_images/11846892-34be00a91ace4244.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第九步图解：

```
 pre = start
 end = pre;
```
初始化pre和end,继续进行循环:

![截屏2022-04-13 下午3.30.56.png](https://upload-images.jianshu.io/upload_images/11846892-a27beea55d836210.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


依次继续执行即可完成翻转。
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

