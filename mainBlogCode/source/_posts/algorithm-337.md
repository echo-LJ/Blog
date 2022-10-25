---
title: leeCode-337： 打家劫舍 III（动态规划）-中等
date: 2022-06-20 14:24:22
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 root 。 除了 root 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 两个直接相连的房子在同一天晚上被打劫 ，房屋将自动报警。 给定二叉树的 root 。返回 在不触动警报的情况下 ，小偷能够盗取的最高金额 


**示例1:**

![截屏2022-06-20 下午2.25.17.png](https://upload-images.jianshu.io/upload_images/11846892-8f3a99d99348ef97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入: root = [3,2,3,null,3,null,1]
输出: 7 
解释: 小偷一晚能够盗取的最高金额 3 + 3 + 1 = 7

```
**示例2:**
![截屏2022-06-20 下午2.25.42.png](https://upload-images.jianshu.io/upload_images/11846892-a08993dea77c0308.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入: root = [3,4,5,1,3,null,1]
输出: 9
解释: 小偷一晚能够盗取的最高金额 4 + 5 = 9

```

## 题解方法一：

`简化一下这个问题:`一棵二叉树，树上的每个点都有对应的权值，每个点有两种状态（选中和不选中），问在不能同时选中有父子关系的点的情况下，能选中的点的最大权值和是多少

我们可以用 f(o) 表示选择 o 节点的情况下，o 节点的子树上被选择的节点的最大权值和；g(o) 表示不选择 o 节点的情况下，o 节点的子树上被选择的节点的最大权值和；l 和 r 代表 oo 的左右孩子。

* 当 o 被选中时，o 的左右孩子都不能被选中，故 o 被选中情况下子树上被选中点的最大权值和为 l 和 r 不被选中的最大权值和相加，即 f(o) = g(l) + g(r)
* 当 o 不被选中时，o 的左右孩子可以被选中，也可以不被选中。对于 o 的某个具体的孩子 x，它对 o 的贡献是 x 被选中和不被选中情况下权值和的较大值。故 g(o) = Math.max(f(l) , g(l))+Math.max(f(r) , g(r)) 

至此，我们可以用哈希表来存 f 和 g 的函数值，用深度优先搜索的办法后序遍历这棵二叉树，我们就可以得到每一个节点的 f 和 g。根节点的 f 和 g 的最大值就是我们要找的答案。

代码实现如下： 
```

var rob = function(root) {
    const f = new Map();
    const g = new Map();

    const dfs = (node) => {
        if (node === null) {
            return;
        }
        dfs(node.left);
        dfs(node.right);
        f.set(node, node.val + (g.get(node.left) || 0) + (g.get(node.right) || 0));
        g.set(node, Math.max(f.get(node.left) || 0, g.get(node.left) || 0) + Math.max(f.get(node.right) || 0, g.get(node.right) || 0));
    }
    
    dfs(root);
    return Math.max(f.get(root) || 0, g.get(root) || 0);
};

```
我们可以做一个小小的优化，我们发现无论是 f(o) 还是 g(o)，他们最终的值只和 f(l)、g(l)、f(r)、g(r) 有关，所以对于每个节点，我们只关心它的孩子节点们的 f 和 g 是多少。我们可以设计一个结构，表示某个节点的 f 和 g 值，在每次递归返回的时候，都把这个点对应的 f 和 g 返回给上一级调用，这样可以省去哈希表的空间。

代码实现如下： 
```

var rob = function(root) {
    const dfs = (node) => {
        if (node === null) {
            return [0, 0];
        }
        const l = dfs(node.left);
        const r = dfs(node.right);
        const selected = node.val + l[1] + r[1];
        const notSelected = Math.max(l[0], l[1]) + Math.max(r[0], r[1]);
        return [selected, notSelected];
    }
    
    const rootStatus = dfs(root);
    return Math.max(rootStatus[0], rootStatus[1]);
}; 

```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
https://github.com/sisterAn/JavaScript-Algorithms/issues/41
https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--22/












