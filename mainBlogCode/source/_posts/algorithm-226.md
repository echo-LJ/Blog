---
title: leeCode-226：翻转二叉树：（递归-深度优先遍历、迭代-广度优先遍历）-初级
date: 2022-06-02 14:15:42
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给你一棵二叉树的根节点 root ，翻转这棵二叉树，并返回其根节点。。


**示例1:**

![demo1.png](https://upload-images.jianshu.io/upload_images/11846892-e2c4deee7e56e53e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

**示例2:**
![demo2.png](https://upload-images.jianshu.io/upload_images/11846892-552a96237f3a4f91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```

输入：root = [2,1,3]
输出：[2,3,1]

```

**示例3:**
```

输入：root = []
输出：[]

```
### 题解方法一： 递归-深度优先遍历（本人自己可以写出来的第二道算法！此处庆祝，热烈鼓掌👏👏）

`从根节点开始，递归地对树进行遍历，并从叶子节点先开始翻转。`

* 如果当前遍历到的节点 \textit{root}root 的左右两棵子树都已经翻转，那么我们只需要交换两棵子树的位置，即可完成以 \textit{root}root 为根节点的整棵子树的翻转。

代码实现如下：
```
var invertTree = function(root) {
    if (root === null) {
        return null;
    }
    const left = invertTree(root.left);
    const right = invertTree(root.right);
    root.left = right;
    root.right = left;
    return root;
};
```

## 题解方法二：迭代-广度优先遍历



代码实现如下：
```
var invertTree = function(root) {
    if(!root) return root
    let stack = [root]
    while(stack.length){
        let node = stack.shift()
        let temp = node.left
        node.left = node.right
        node.right = temp
        if(node.left){
            stack.push(node.left)
        }
        if(node.right){
            stack.push(node.right)
        }
    }
    return root
};

};

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/invert-binary-tree/solution/dong-hua-yan-shi-liang-chong-shi-xian-226-fan-zhua/
