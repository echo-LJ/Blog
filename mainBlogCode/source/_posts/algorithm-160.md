---
title: leeCode-160： 相交链表(遍历、双指针两种解法)-中级
date: 2022-04-21 18:24:09
tags: 算法
---


<meta name="referrer" content="no-referrer"/>



## 题目描述： 

![截屏2022-04-21 下午6.25.00.png](https://upload-images.jianshu.io/upload_images/11846892-6fdd2ff686e3d1a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**示例1:**

![截屏2022-04-21 下午6.25.53.png](https://upload-images.jianshu.io/upload_images/11846892-503db582fcf6d62c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```
**示例2:**

![截屏2022-04-21 下午6.26.26.png](https://upload-images.jianshu.io/upload_images/11846892-396b27a709551bc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Intersected at '2'
解释：相交节点的值为 2 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [1,9,1,2,4]，链表 B 为 [3,2,4]。
在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

**示例3:**
![截屏2022-04-21 下午6.26.51.png](https://upload-images.jianshu.io/upload_images/11846892-ff87efb3b0aae8bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。
由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
这两个链表不相交，因此返回 null 。
```

⚠️！！ 本题的所有解法需要对141题有一定的理解！！

自己真正意义上写出的第一道算法，开心啊！！
## 题解方法一：遍历

`解题思路:`
判断两个链表是否相交，可以使用哈希集合存储链表节点。

首先遍历链表 headA，并将链表 headA 中的每个节点加入哈希集合中。然后遍历链表 headB，对于遍历到的每个节点，判断该节点是否在哈希集合中：

如果当前节点不在哈希集合中，则继续遍历下一个节点；

如果当前节点在哈希集合中，则后面的节点都在哈希集合中，即从当前节点开始的所有节点都在两个链表的相交部分，因此在链表 headB 中遍历到的第一个在哈希集合中的节点就是两个链表相交的节点，返回该节点。

如果链表 headB 中的所有节点都不在哈希集合中，则两个链表不相交，返回 null。
**题目分析**


以这种解法为例
```

var getIntersectionNode = function(headA, headB) {
    const visited = new Set();
    let temp = headA;
    while (temp !== null) {
        visited.add(temp);
        temp = temp.next;
    }
    temp = headB;
    while (temp !== null) {
        if (visited.has(temp)) {
            return temp;
        }
        temp = temp.next;
    }
    return null;
};

```


## 题解方法二：双指针


`解题思路:`
1、判断两个链表是否为空，如果有为空，则不存在相交节点，返回null
2、构建两个节点指针 A​ , B 分别指向两链表头节点 headA , headB ，做如下操作：
   * 指针 A 先遍历完链表 headA ，再开始遍历链表 headB ，当走到 node 时，共走步数为： a + (b - c)
   * 指针 B 先遍历完链表 headB ，再开始遍历链表 headA ，当走到 node 时，共走步数为： b + (a - c)
   * 此时指针 A , B 重合，并有两种情况： a + (b - c) = b + (a - c)
3、若两链表有公共尾部（即c>0）:指针 A , B 同时指向「第一个公共节点」node。
4、若两链表 无 公共尾部 (即 c = 0c=0 ) ：指针 A , B 同时指向 nullnull 。
5、因此返回A即可。

代码实现如下：

```
var getIntersectionNode = function(headA, headB) {
    if (headA === null || headB === null) {
        return null;
    }
    let pA = headA, pB = headB;
    while (pA !== pB) {
        pA = pA === null ? headB : pA.next;
        pB = pB === null ? headA : pB.next;
    }
    return pA;
};
};
```
下面我们对`while`循环进行图解:
```
while (pA !== pB) {
        pA = pA === null ? headB : pA.next;
        pB = pB === null ? headA : pB.next;
    }
```
![截屏2022-04-21 下午6.38.27.png](https://upload-images.jianshu.io/upload_images/11846892-102c67243671ec3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截屏2022-04-21 下午6.40.01.png](https://upload-images.jianshu.io/upload_images/11846892-eeb4ee86a5bbcc2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截屏2022-04-21 下午6.42.39.png](https://upload-images.jianshu.io/upload_images/11846892-49f582098b82732d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截屏2022-04-21 下午6.43.03.png](https://upload-images.jianshu.io/upload_images/11846892-94e50806b41df48e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

