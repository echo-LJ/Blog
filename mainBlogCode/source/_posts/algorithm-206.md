---
title: 206反转链表
date: 2022-04-11 14:44:59
tags:
---
<meta name="referrer" content="no-referrer"/>
什么是链表？链表结构参考：


## 题目描述：给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。

示例1:

![demo1.png](https://upload-images.jianshu.io/upload_images/11846892-e379a9cc3764efd8.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
```

示例 3：
```

输入：head = []
输出：[]

````

## 题解方法一：迭代

`在遍历链表时，将当前节点的 next 指针改为指向前一个节点。由于节点没有引用其前一个节点，因此必须事先存储其前一个节点。在更改引用之前，还需要存储后一个节点。最后返回新的头引用。`

代码实现如下：
```
var reverseList = function(head) {
    let prev = null;
    let curr = head;
    while (curr) {
        const next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
};
```

下面通过图解 来实现 反转链表：

第一行代码图解：

```
 var reverseList = function(head) { //1
```

我们以示例1为例，进行解析题目，当前链表有1、2、3、4、5个元素，链尾[缺链接]最后跟着一个null,所以即将要被反转的链表结构如下：

![截屏2022-04-11 下午3.48.54.png](https://upload-images.jianshu.io/upload_images/11846892-ae51cab1329393ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


第二行代码图解：
```
 let prev = null;   // 2
```
将null 赋值给prev, 即prev 指向null, 如图所示：

![截屏2022-04-11 下午3.52.00.png](https://upload-images.jianshu.io/upload_images/11846892-7047e1b88c9f2346.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第三行代码图解：
```
 let curr = head; //3
```
将链表head赋值给curr,即curr指向head链表, 如图所示：

![截屏2022-04-11 下午3.53.54.png](https://upload-images.jianshu.io/upload_images/11846892-5a23ea506a26afb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

循环部分代码图解：
```
  while (curr) {  //4
        const next = curr.next; //5
        curr.next = prev; //6
        prev = curr; //7
        curr = next; //8
    }
```
循环部分是链表反转的核心部分，我们先走一遍循环，图解分析一波。

因为curr指向了head，head不为null，所以进入循环。先来看第5行：

第五行代码图解：
```
 const next = curr.next; //5
```
将curr.next复制给变量next，即next指向curr的下一个节点，如图所示：

![截屏2022-04-11 下午3.58.13.png](https://upload-images.jianshu.io/upload_images/11846892-b62650e4fa713e6f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第六行代码图解：
```
 curr.next = prev; //6
```
将prev赋值给curr.next， 因为prev指向null,即curr指向了null,但前各个变量如图所示：

![截屏2022-04-11 下午4.03.42.png](https://upload-images.jianshu.io/upload_images/11846892-deab58010f4a1f98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第七行代码图解：
```
 prev = curr; //7
```
将curr赋值给prev,即prev指向curr,如图所示：

![截屏2022-04-11 下午4.05.35.png](https://upload-images.jianshu.io/upload_images/11846892-53fd2349465757bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第八行代码图解：
```
 curr = next;; //8
```
将next赋值给curr,即curr指向next,如图所示：


![截屏2022-04-11 下午4.07.01.png](https://upload-images.jianshu.io/upload_images/11846892-d30599903d8e870b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

至此，第一遍循环执行结束啦，回到while循环，因为curr依旧不为null， 我们需要重复上述循环继续解析它；

```
const next = curr.next; //5
curr.next = prev; //6
prev = curr; //7
curr = next; //8
```
此处省略中间3遍5-8行代码解析，继续执行第5遍5-8行代码图解，如下所示：

以此可得图解：

![截屏2022-04-11 下午4.14.27.png](https://upload-images.jianshu.io/upload_images/11846892-9cf9cd948cb52607.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-04-11 下午4.16.23.png](https://upload-images.jianshu.io/upload_images/11846892-625dad964b02505b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-04-11 下午4.16.56.png](https://upload-images.jianshu.io/upload_images/11846892-bbcfc838b0d6677c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-04-11 下午4.17.25.png](https://upload-images.jianshu.io/upload_images/11846892-01867ef0a012d558.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

到了此处，我们发现curr已经为null了，跳出了while循环，prev已经指向了向标链表的反转了，所以执行到第九行，反转链表功能即可实现：

```
return prev； //9
```


题型讲解参考；https://juejin.cn/post/6844904058562543623

## 题解方法二：递归

`递归版本稍微复杂一些，其关键在于反向工作。迭代方式从前面1开始依次往后处理，而递归方式恰恰相反，它先循环找到最后面的指向的数5，从而从5开始依次处理翻转整个链表`

解体思路： 首先设置一个新的指针newHead作为翻转后的链表的链头。由于整个链表反转之后的链头就是当前链表的链尾的数（最后一个数），所以整个过程newHead指针一直指向存放5的地址空间。


代码实现如下：
```
var reverseList = function(head) {
    if (head == null || head.next == null) {
        return head;
    }
    const newHead = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    return newHead;
};

```

下面通过代码拆分解析如下：

```
var reverseList = function(head) {  //  head： 1->2->3->4->5->null 
    if (head == null || head.next == null) {
        return head;
    }
    const newHead = function(head) {  //  head： 2->3->4->5->null  // 执行顺序11: newHead: 5的地址空间
        if (head == null || head.next == null) {
            return head;
        }
        const newHead = function(head) {  //  head： 3->4->5->null  // 执行顺序8: newHead: 5的地址空间
            if (head == null || head.next == null) {
                return head;
            }
            const newHead = function(head) {  //  head： 4->5->null  // 执行顺序5: newHead: 5的地址空间
                if (head == null || head.next == null) {
                    return head;
                }
                const newHead = function(head) {  //  执行顺序1: 此处newHead 5->null  执行到if后不向下执行 head： 5->null     newHead:   5的地址空间
                    if (head == null || head.next == null) {
                        return head;
                    }
                };
                head.next.next = head; // 执行顺序2: 4.next.next = 4 即 5.next = 4; head： 5->4
                head.next = null; // 执行顺序3:   4.next = null,  head： 5->4->null
                return newHead; // 执行顺序4: newHead指向节点: 5的地址空间  
            };
            head.next.next = head;  // 执行顺序6: 3.next.next = 3 即 4.next = 3; head： 5->4->3
            head.next = null;  // 执行顺序7:   3.next = null,  head： 5->4->3->null
            return newHead; // 执行顺序8: newHead指向节点: 5的地址空间  
        };
        head.next.next = head; // 执行顺序9: 2.next.next = 2 即 3.next = 2; head： 5->4->3->2
        head.next = null; // 执行顺序10:   2.next = null,  head： 5->4->3->2->null
        return newHead; // 执行顺序11: newHead指向节点: 5的地址空间  
    };
    head.next.next = head; // 执行顺序12: 1.next.next = 1 即 2.next = 1; head： 5->4->3->2->1
    head.next = null; // 执行顺序13:   1.next = null,  head： 5->4->3->2->1->null
    return newHead;  // 执行顺序14: newHead指向节点:5的地址空间 
};

```

中心思想讲解： 

* 设置一个新的指针[newHead]作为翻转后的链表的链头。由于整个链表翻转之后的头就是最后一个数，所以整个过程newHead指针一直存放5的地址空间。
* head指针逐层返回的时候依次将H的指针复制给head.next.next， 并且一定要将head.next设置为null, 即断开现在指针的链接，否则会新的链表会形成闭环。

题型讲解参考:
1、https://blog.csdn.net/FX677588/article/details/72357389

2、https://leetcode-cn.com/problems/reverse-linked-list/solution/dong-hua-yan-shi-206-fan-zhuan-lian-biao-by-user74/