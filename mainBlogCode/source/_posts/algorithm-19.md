---
title: leeCode-19：  删除链表的倒数第 N 个结点： (链表长度、栈存储、双指针三种解法)-中等
date: 2022-04-26 11:15:43
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

**示例1:**

![截屏2022-04-26 上午11.17.58.png](https://upload-images.jianshu.io/upload_images/11846892-5e32bed5083b7760.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

**示例2:**

```
输入：head = [1], n = 1
输出：[]
```

**示例3:**

```
输入：head = [1,2], n = 1
输出：[1]
```

## 题解方法一：计算链表长度

**`解题思路`**
* 从头节点开始对链表进行遍历，得到链表的长度L.
* 从头节开始进行二次遍历，当遍历到L-n+1,得到需要删除的节点。
* 修改指针，即可完成删除操作。

![截屏2022-04-26 上午11.20.17.png](https://upload-images.jianshu.io/upload_images/11846892-ad099cb42bfd5f71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码实现如下： 
```
var removeNthFromEnd = function(head, n) {
    let pre = head;
    let len = getLength(head);
    if( n>len ){
        return head.next;
    }
    if( n<=len && n >0){
        let dummyNode = new ListNode();
        dummyNode.next = head;
        let pre = dummyNode;
        
        for(let i =0;i<len-n;i++){
            pre = pre.next;
        }
        pre.next = pre.next.next;
        return dummyNode.next;
    }

};

var getLength = function(head) {
    let length = 0;
    while(head !== null) {
        ++length;
        head = head.next;
    }
    return length
}
```


## 题解方法二：栈

**`解题思路`**
* 从头节点开始对链表进行遍历，存入到栈中[先进后出]。
* 弹出栈的第n个节点，就是要删除的节点，
* 栈顶节点就是要删除节点的前驱节点。


![截屏2022-04-26 上午11.37.46.png](https://upload-images.jianshu.io/upload_images/11846892-682b5d51e7bf0a09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

栈代码实现如下：

```
var removeNthFromEnd = (head, n ) => {
    let dummyNode = new ListNode(-1);
    dummyNode.next = head;
    let stack = []
    let cur = dummyNode;
    while(cur !== null) {
        stack.push(cur);
        cur = cur.next;
    }
    for (let i = 0; i< n ; i++) {
        stack.pop();
    }
    let prev = stack.pop();
    prev.next = prev.next.next;
    let ans = dummyNode.next;
    return ans
}
```

## 题解方法二：双指针

**`解题思路`**
* 快指针先移动n步
* 快慢指针同时移动， 快指针移动到结尾结束， 慢指针当前在倒数第n-1个节点
* 慢指针跳过倒数第n个节点


![截屏2022-04-29 下午3.39.05.png](https://upload-images.jianshu.io/upload_images/11846892-cc4c2eda5e5f6ef3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


双指针实现代码实现如下：

```
var removeNthFromEnd = (head, n ) => {
    let dummyNode = new ListNode(-1);
    dummyNode.next = head;
    let first= head;
    let second = dummyNode;
    for (let i = 0; i< n ; i++) {
        first = first.next
    }
    while(first !==null) {
        first = first.next 
        second = second.next;
    } 
    second.next = second.next.next;
    let ans = dummyNode.next;
    return ans
}
```
