---
title: leeCode-105： 从前序与中序遍历序列构造二叉树 (递归)-中等
date: 2022-06-16 13:39:28
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。


**示例1:**

![截屏2022-06-16 下午1.41.37.png](https://upload-images.jianshu.io/upload_images/11846892-d0049b64d723ec7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
输出: [3,9,20,null,null,15,7]

```
**示例2:**

```
输入: preorder = [-1], inorder = [-1]
输出: [-1]

```

## 题解方法一：递归

`解题思路:`

* 前序遍历：根左右
* 中序遍历：左根右
* 我们通过前序遍历可以知道二叉树的根
* 知道二叉树的根，根据中序遍历，我们可以知道根的左右子树的中序遍历及左右子树节点数目
* 知道左右子树的节点数目，结合前序遍历，我们就知道左右子树的前序遍历

![截屏2022-06-16 下午1.46.54.png](https://upload-images.jianshu.io/upload_images/11846892-77b744d2837eda35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![截屏2022-06-16 下午1.47.10.png](https://upload-images.jianshu.io/upload_images/11846892-a083c7ab4ee58e65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![截屏2022-06-16 下午1.47.40.png](https://upload-images.jianshu.io/upload_images/11846892-bb717beec789952c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


递归方法代码实现如下1： 
```
var buildTree = function(preorder, inorder) {
    if (preorder.length === 0) return null; // 在数组长度为0时结束递归
    const root = new TreeNode(preorder[0]); // 前序遍历第一个元素为根节点
    const mid = inorder.indexOf(preorder[0]); // 获取根节点中序遍历中的索引
    // 根据索引来切割数组 对子数数组继续递归
    root.left = buildTree(preorder.slice(1, mid + 1), inorder.slice(0, mid));
    root.right = buildTree(preorder.slice(mid + 1), inorder.slice(mid + 1));
    return root;
};
```
递归方法代码实现如下2： 
```

var buildTree = (preorder, inorder) => {
    let map = new Map()
    for (let i = 0; i < inorder.length; i++) {
        map.set(inorder[i], i)
    }
    return helper(preorder, 0, preorder.length - 1, inorder, 0, inorder.length - 1, map)
}

function helper(preorder, p_start, p_end, inorder, i_start, i_end, map) {
    if (p_start > p_end) return null // preorder为空
    let rootVal = preorder[p_start] // 根节点的值
    let root = new TreeNode(rootVal) // 根节点
    let mid = map.get(rootVal) // 根节点在inorder的位置
    let leftNum = mid - i_start // 左子树的节点数

    root.left = helper(preorder, p_start + 1, p_start + leftNum, inorder, i_start, mid - 1, map)
    root.right = helper(preorder, p_start + leftNum + 1, p_end, inorder, mid + 1, i_end, map)
    return root
}
```


 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://github.com/sisterAn/JavaScript-Algorithms/issues/41
https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--22/











