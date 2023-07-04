---
title: leeCode-23： 合并K个升序链表： (顺序合并、分而治之两种解法)-困难
date: 2022-04-18 13:40:39
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 给你一个链表数组，每个链表都已经按升序排列。 请你将所有链表合并到一个升序链表中，返回合并后的链表。


**示例1:**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例2:**

```
输入：lists = []
输出：[]
```

**示例3:**

```
输入：lists = [[]]
输出：[]

```


前置知识：需要对合并两个有序链表的解法有一定的理解和掌握:[可参考21题](https://echo-lj.github.io/2022/04/14/algorithm-21/)


## 题解方法一：顺序合并


`解题思路:`
* 1、用一个变量ans来维护以及合并的链表，
* 2、第i次循环把第i个链表和ans合并，答案保存到ans中。

⚠️：只要对合并两个有序链表的解法有一定的理解和掌握，第一种解题思路很容易理解，此处不进行图解分析，直接展示代码实现。

代码实现如下：
```
var mergeKLists = function(lists) {
    let ans = null;
    for (let i = 0; i< lists.length; i++){
        ans = mergeTwoLists(ans, lists[i])
    }
    return ans;
};

// mergeTwoLists 这个方法就是合并两个有序链表的解法
var mergeTwoLists = function(l1, l2) {
    if (l1 === null){
        return l2
    } else if (l2 === null){
        return l1
    }
    if (l1.val < l2.val) {
        l1.next = mergeTwoLists(l1.next, l2)
        return l1
    } else {
        l2.next = mergeTwoLists(l1, l2.next)
        return l2
    }
};

```


复杂度分析：（时间复杂度和空间复杂度需要重新理解，待整理）



## 题解方法二：分治合并



![截屏2022-04-18 下午4.13.31.png](https://upload-images.jianshu.io/upload_images/11846892-97541bc443032697.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```

var mergeKLists = function(lists) {
    // 提前退出
    if (lists.length === 0) return null
    return mergeList(lists, 0, lists.length - 1)

};

// 并归排序
function mergeList(list, start, end) {
  	// 相等则k为奇数个时
    if (start === end) return list[start]
  	// 计算中间值
    const mid =  Math.floor((end + start) /2)
    const leftList = mergeList(list, start, mid)
    const rightList = mergeList(list, mid + 1, end)
    return merge(leftList, rightList)
}

// 合并两个升序链表
function merge(list1, list2) {
    // 虚拟头结点
    let dummy = new ListNode(0)
    // 当前结点
    let temp = dummy
    while(list1 && list2) {
        if (list1.val <= list2.val) {
            temp.next = list1
            list1 = list1.next
        } else {
            temp.next = list2
            list2 = list2.next
        }
        temp = temp.next
    }
  	// 处理剩余的
    temp.next = list1 ? list1 : list2
    return dummy.next
}
    
```

接下来我们以示例
```
const lists = [[1,4,5],[1,3,4],[2,6],[4,6,7,8],[2,3,7,8],[3,5,7,8]]
```
下面通过代码拆分解析如下：

```
var mergeKLists = function(lists) { //第1步：lists = [[1,4,5],[1,3,4],[2,6],[4,6,7,8],[2,3,7,8],[3,5,7,8]]
    // 提前退出
    if (lists.length === 0) return null
    return mergeList(lists, 0, lists.length - 1) //第2步： 5 =lists.length - 1

};

// 并归排序
function mergeList(list, start, end) { // start = 0, end = 5
  	// 相等则k为奇数个时
    if (start === end) return list[start]
  	// 计算中间值
    const mid = Math.floor((end + start) /2) //第3步： mid = 2
    const leftList = mergeList(list, start, mid) { // 第4步： start = 0 end = 2 //  第15步：leftList = merge([1,1,3,4,4,5],[2,6]) = [1,1,2,3,4,4,5,6]
            // 相等则k为奇数个时
            if (start === end) return list[start]
            // 计算中间值
            const mid =  Math.floor((end + start) /2) //第5步： mid = 1
            const leftList = mergeList(list, start, mid) { // 第6步： start = 0 end = 1  //  第12步：leftList = merge(list[0], list[1]) = = [1,1,3,4,4,5]
                    // 相等则k为奇数个时
                    if (start === end) return list[start]
                    // 计算中间值
                    const mid =  Math.floor((end + start) /2) //第7步： mid = 0
                    const leftList = mergeList(list, start, mid){ // 第8步： start = 0 end = 1
                        // 相等则k为奇数个时
                        if (start === end) return list[start] // 第9步： return list[0]
                    }
                    const rightList = mergeList(list, mid + 1, end)  // 第10步： mid+1 = 1 end = 1
                    return merge(leftList, rightList) //  第11步：merge(list[0], list[1]) = merge([1,4,5],[1,3,4]) = [1,1,3,4,4,5]
            }
            const rightList = mergeList(list, mid + 1, end) // 第13步 mid+1 = 2 end = 2 rightList = [2,6]
            return merge(leftList, rightList) // 第14步： merge([1,1,3,4,4,5],[2,6]) = [1,1,2,3,4,4,5,6]
    }
    const rightList = mergeList(list, mid + 1, end) //第15步 执行方式如同leteList  mid + 1 = 3, ende = 5 3和4组成left 5是right 
    return merge(leftList, rightList)
}

// 合并两个升序链表
function merge(list1, list2) {
    // 虚拟头结点
    let dummy = new ListNode(0)
    // 当前结点
    let temp = dummy
    while(list1 && list2) {
        if (list1.val <= list2.val) {
            temp.next = list1
            list1 = list1.next
        } else {
            temp.next = list2
            list2 = list2.next
        }
        temp = temp.next
    }
  	// 处理剩余的
    temp.next = list1 ? list1 : list2
    return dummy.next
}

```

![截屏2022-04-18 下午6.57.54.png](https://upload-images.jianshu.io/upload_images/11846892-c78703db4610eb3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)