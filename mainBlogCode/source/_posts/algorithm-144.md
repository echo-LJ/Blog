---
title: leeCode-144： 二叉树的前序遍历： (迭代、递归两种解法)-简单
date: 2022-04-24 16:25:41
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

## 题目描述： 给你二叉树的根节点 root ，返回它节点值的 前序 遍历。

**示例1:**

![屏幕快照 2022-04-24 下午4.27.55.png](https://upload-images.jianshu.io/upload_images/11846892-ecf6dc20ad7a9e22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,null,2,3]
输出：[1,2,3]
```

**示例2:**

```
输入：root = []
输出：[]
```

**示例3:**

```
输入：root = [1]
输出：[1]
```
**示例4:**
![屏幕快照 2022-04-24 下午4.29.16.png](https://upload-images.jianshu.io/upload_images/11846892-621ab535519d00b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,2]
输出：[1,2]
```

**示例5:**

![屏幕快照 2022-04-24 下午4.29.39.png](https://upload-images.jianshu.io/upload_images/11846892-317cb88b31fa5df5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,null,2]
输出：[1,2]
```


## 题解方法一：递归法

解题思路：二叉树的前序遍历：按照访问根节点——左子树——右子树的方式遍历这棵树，而在访问左子树或者右子树的时候，我们按照同样的方式遍历，直到遍历完整棵树。因此整个遍历过程天然具有递归的性质，我们可以直接用递归函数来模拟这一过程。

代码实现如下：
```
var preorderTraversal = (root) => {
  let arr = []
  preorder(root, arr);
  return arr
}
var preorder = (root, arr) => {
  if (root === null) return
  arr.push(root.val);
  preorder(root.left, arr)
  preorder(root.right, arr)
}
```

## 题解方法一：迭代

解题思路：我们也可以用迭代的方式实现方法一的递归函数，两种方式是等价的，区别在于递归的时候隐式地维护了一个栈，而我们在迭代的时候需要显式地将这个栈模拟出来，其余的实现与细节都相同。

代码实现如下：
```
var preorderTraversal = function(root) {
    if (!root) return []
    const stack = [], res = []
    stack.push(root)
    while (stack.length) {
        const curr = stack.pop()
        res.push(curr.val)
        if (curr.right) stack.push(curr.right)
        if (curr.left) stack.push(curr.left)
    }
    return res
};
```
下面进行图解分析：

```
stack.push(root)
```
![屏幕快照 2022-04-24 下午5.20.25.png](https://upload-images.jianshu.io/upload_images/11846892-0753974f29e5145b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

stack.length不为0 ，进入while循环
执行第一次循环之后：

![屏幕快照 2022-04-24 下午5.22.23.png](https://upload-images.jianshu.io/upload_images/11846892-83c93dca0a1ef3a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行第二次循环之后：
![屏幕快照 2022-04-24 下午5.24.18.png](https://upload-images.jianshu.io/upload_images/11846892-56773f3caac0d013.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行第3次循环之后：
![屏幕快照 2022-04-24 下午5.25.05.png](https://upload-images.jianshu.io/upload_images/11846892-bbfd616c532c7b42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行第4次循环之后：

![屏幕快照 2022-04-24 下午5.25.46.png](https://upload-images.jianshu.io/upload_images/11846892-aec07ae65b31f893.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行第5次循环之后：
![屏幕快照 2022-04-24 下午5.26.54.png](https://upload-images.jianshu.io/upload_images/11846892-3f4036c490b19e34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行第6次循环之后：

![屏幕快照 2022-04-24 下午5.27.45.png](https://upload-images.jianshu.io/upload_images/11846892-5cfe4c37d9b9dcdc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


结束循环，执行完毕！！



