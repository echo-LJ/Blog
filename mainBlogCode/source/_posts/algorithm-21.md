---
title: leeCode-21： 合并两个有序链表： (迭代、递归两种解法)-简单
date: 2022-04-14 13:51:14
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 



可参考链接：https://leetcode-cn.com/problems/merge-two-sorted-lists/solution/yi-kan-jiu-hui-yi-xie-jiu-fei-xiang-jie-di-gui-by-/

**示例1:**
![截屏2022-04-14 下午1.54.33.png](https://upload-images.jianshu.io/upload_images/11846892-0f2e389a502cec00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

**示例2:**

```
输入：l1 = [], l2 = []
输出：[]
```

**示例3:**

```
输入：l1 = [], l2 = [0]
输出：[0]

```

## 题解方法一：递归


`解题思路:`
* 1、终止条件：两条链表分别命名l1和l2,当l1为空或l2为空时结束
* 2、返回值：每一层调用都返回排序好的链表头
* 3、 本级递归内容： 如果l1的val值更小，则将l1.next 与排序好的链表头相接，l2同理
* 4、 O(m+n)，mm 为 l1的长度，nn 为 l2 的长度

代码实现如下：
```
var mergeTwoLists = function(l1, l2) {
    if(l1 === null){
        return l2;
    }
    if(l2 === null){ //注意此处不要写错成l2.val
        return l1;
    }
    if(l1.val < l2.val){
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    }else{
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
};

```
以示例1 为例，代码拆分理解如下:
```

var mergeTwoLists = function(l1, l2) {// 执行第一步： l1 = [1,2,4] l2 = [1,3,4]
    if(l1 === null){
        return l2;
    }
    if(l2 === null){
        return l1;
    }
    if(l1.val < l2.val){ 
        l1.next = mergeTwoLists(l1.next, l2);
        return l1;
    }else{ // 执行第二步：l1.val = l2.val = 1
        l2.next = mergeTwoLists = function(l1, l2.next) {  // 执行第三步： l1 = [1,2,4] l2.next =  [3,4] // 执行第21步：  l2 = [1, 1, 2, 3, 4, 4]
                    if(l1 === null){
                        return l2;
                    }
                    if(l2 === null){
                        return l1;
                    }
                    if(l1.val < l2.val){ // 执行第四步：l1.val = 1 l2.val = 3
                        l1.next = mergeTwoLists(l1.next, l2) { // 执行第五步：l1.next = 2 l2.val = 3 // 执行第19步：  l1 = [1, 2, 3, 4, 4]
                                    if(l1 === null){
                                        return l2;
                                    }
                                    if(l2 === null){
                                        return l1;
                                    }
                                    if(l1.val < l2.val){ // 执行第六步：l1.next = 2 l2.val = 3
                                        l1.next = mergeTwoLists(l1.next, l2){ // 执行第七步：l1.next = 4 l2.val = 3 // 执行第17步：  l1 = [2, 3, 4, 4]
                                                if(l1 === null){
                                                    return l2;
                                                }
                                                if(l2 === null){
                                                    return l1;
                                                }
                                                if(l1.val < l2.val){ 
                                                    l1.next = mergeTwoLists(l1.next, l2);
                                                    return l1;
                                                }else{ // 执行第八步：l1.val = 4 l2.val = 3
                                                    l2.next = mergeTwoLists(l1, l2.next){ // 执行第九步：l1 = 4 l2.next = 4  // 执行第15步：  l2 = [3, 4, 4]
                                                                if(l1 === null){
                                                                    return l2;
                                                                }
                                                                if(l2 === null){
                                                                    return l1;
                                                                }
                                                                if(l1.val < l2.val){ 
                                                                    l1.next = mergeTwoLists(l1.next, l2);
                                                                    return l1;
                                                                }else{ // 执行第十步：l1.val = 4 l2.val = 4
                                                                    l2.next = mergeTwoLists(l1, l2.next){ // 执行第十一步：l1 = 4 l2.next = null // 执行第十三步： l2.next = 4
                                                                            if(l1 === null){
                                                                                return l2;
                                                                            }
                                                                            if(l2 === null){ // 执行第十二步： return l1 = 4
                                                                                return l1;
                                                                            }
                                                                    }
                                                                    return l2; // 执行第十四步： return l2 = [4, 4]
                                                                }
                                                    return l2; // 执行第16步：  l2 = [3, 4, 4]
                                                }
                                        return l1; // 执行第18步：  l1 = [2, 3, 4, 4]
                                    }else{
                                        l2.next = mergeTwoLists(l1, l2.next);
                                        return l2;
                                    }
                                };
                        return l1; // 执行第20步：  l1 = [1, 2, 3, 4, 4]
                    }else{
                        l2.next = mergeTwoLists(l1, l2.next);
                        return l2;
                    }
        return l2; // 执行第22步：  l2 = [1, 1, 2, 3, 4, 4]
    }
};

```
执行到第22步结束循环；


## 题解方法二：迭代

`解题思路:`
* 1、使用 dummy->next 来保存需要返回的头节点
* 2、判断 l1 和 l2 哪个更小，就把这个节点接到下一个 使用指向指针的指针 pp 用来存储更小的一边的指针 在帮助 dummy 连接之后，还可以控制更小的 l1 或 l2 向后移动
* 3、 直到有一边为 null ，即可将另一边剩余的都接上


代码实现如下：
```
var mergeTwoLists = function(l1, l2) {
    let dummyNode = new ListNode(-1);
    let prev = dummyNode
    while(l1 !== null && l2 !==null) {
        if (l1.val < l2.val) {
            prev.next = l1
            l1 = l1.next
        } else {
            prev.next = l2
            l2 = l2.next
        }
        prev = prev.next
    }
    prev.next = l1 === null ? l2:l1;
    return dummyNode.next
};

```

以示例1 为例，下面通过图解 来实现 合并有序链表：

* 第一步图解：

```
// 因为头节点有可能发生变化，使用虚拟头节点可以避免复杂的分类讨论
    let dummyNode = new ListNode(-1);
    let prev = dummyNode
```
创建了一个节点，它的指针指向head：

`举例解释如下`：dummy_node可以把它理解为一个"哨兵"，因为我们的头结点可能会涉及到反转，当反转之后，原来的头结点就不是返回后的头结点了，举个例子,[5,3]进行1，2的反转，此时head指向的是5节点，而反转之后结果为[3,5]，而此时的head还是指向5，所以我们需要一个哨兵，来坚定不移的站在头结点前，这样不管他如何反转，哨兵dummy_node指向的下一个节点dummy_node.next就是我们要的头结点

![截屏2022-04-14 下午3.17.22.png](https://upload-images.jianshu.io/upload_images/11846892-0a3f35fb2d4f26d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第二步图解：

```
while(l1 !== null && l2 !==null) { // l1 = 1; l2 = 1
        if (l1.val < l2.val) {
            prev.next = l1
            l1 = l1.next
        } else {
            prev.next = l2
            l2 = l2.next
        }
```
进入for循环, l1.val = 1 l2.val = 1;执行else内部代码块，  prev.next = 1    l2 = 4

![截屏2022-04-14 下午3.22.52.png](https://upload-images.jianshu.io/upload_images/11846892-ec05ebd340dca7b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 第三步图解：

```
prev = prev.next
```

![截屏2022-04-14 下午3.18.09.png](https://upload-images.jianshu.io/upload_images/11846892-e64e6d621e023e31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 第四步图解：

```
while(l1 !== null && l2 !==null) { 
        if (l1.val < l2.val) {
            prev.next = l1
            l1 = l1.next
        } else {
            prev.next = l2
            l2 = l2.next
        }
        prev = prev.next
    }
```
继续执行while循环，多次执行while循环图解如下：
![第二次while循环.png](https://upload-images.jianshu.io/upload_images/11846892-e0cd437406e2b230.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![第三次while循环.png](https://upload-images.jianshu.io/upload_images/11846892-7fd2904728717e15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![第四次while循环.png](https://upload-images.jianshu.io/upload_images/11846892-7d387f478be0e96b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![第五次while循环.png](https://upload-images.jianshu.io/upload_images/11846892-9e2487bcd85ecfa0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

到这里 l2 ==null 循环结束！

* 第五步图解：

```
prev.next = l1 === null ? l2:l1;
```
此时l1 !== null, l2 = null; 则prev继续向后连接l1， 即可完成两个链表的合并。

![截屏2022-04-14 下午3.34.11.png](https://upload-images.jianshu.io/upload_images/11846892-5df11da9dd3e79f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时可以看到dummyNode.next则两个链表。