
---
title: leeCode-543：二叉树的直径： -中等
date: 2022-06-05 15:29:45
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

**示例1:**


给定二叉树
![demo1.png](https://upload-images.jianshu.io/upload_images/11846892-4305e8412de2a7d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。


### 题解方法一： 深度优先搜索

**`题目分析：`**
* *首先我们知道一条路径的长度为该路径经过的节点数减一，所以求直径（即求路径长度的最大值）等效于求路径经过节点数的最大值减一。
* 而任意一条路径均可以被看作由某个节点为起点，从其左儿子和右儿子向下遍历的路径拼接得到。

![ans.png](https://upload-images.jianshu.io/upload_images/11846892-175c74a1866db942.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 我们可以知道路径 [9, 4, 2, 5, 7, 8] 可以被看作以 22 为起点
* 假设我们知道对于该节点的左儿子向下遍历经过最多的节点数 LL （即以左儿子为根的子树的深度） 和其右儿子向下遍历经过最多的节点数 RR （即以右儿子为根的子树的深度），那么以该节点为起点的路径经过节点数的最大值即为 L+R+1L+R+1
* 最后的算法流程为：我们定义一个递归函数 depth(node) 计算 经过的最大节点数，函数返回该节点为根的子树的深度。先递归调用左儿子和右儿子求得它们为根的子树的深度 LL 和 RR ，则该节点为根的子树的深度即为 max(L,R)+1。该节点的  经过的最大节点数  经过的最大节点数值为 经过的最大节点数L+R+1。递归搜索每个节点并设一个全局变量 ans 记录 经过的最大节点数的最大值，最后返回 ans-1 即为树的直径。

代码实现如下：
```
var diameterOfBinaryTree = function(root) {
    
    
        var deep = function(root) {
        if(root ==null) return 0 
        let L= deep(root.left)
        let R= deep(root.right)
        ans = Math.max( L+R,ans)
        return Math.max(L, R) +1
    }
    let ans = 0
    deep(root)
    return ans 
};

```


 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/symmetric-tree/solution/dui-cheng-er-cha-shu-by-leetcode-solution/

