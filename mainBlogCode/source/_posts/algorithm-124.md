---
title: leeCode-124： 二叉树中的最大路径和：(递归)-困难
date: 2022-06-13 13:57:09
tags: 算法
---
<meta name="referrer" content="no-referrer"/>

## 题目描述：路径 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中 至多出现一次 。该路径 至少包含一个 节点，且不一定经过根节点，路径和 是路径中各节点值的总和，给你一个二叉树的根节点 root ，返回其 最大路径和 。

⚠️⚠️：这个题属实有点难啊！幸亏找到了一个逻辑讲述比较清晰的题解。
**示例1:**


![截屏2022-06-13 下午2.00.05.png](https://upload-images.jianshu.io/upload_images/11846892-2f75b887ea164dd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,2,3]
输出：6
解释：最优路径是 2 -> 1 -> 3 ，路径和为 2 + 1 + 3 = 6
```
**示例2:**
![截屏2022-06-13 下午2.01.18.png](https://upload-images.jianshu.io/upload_images/11846892-04abb51ec6cb6f4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [-10,9,20,null,null,15,7]
输出：42
解释：最优路径是 15 -> 20 -> 7 ，路径和为 15 + 20 + 7 = 42
```

**示例3:**
```
输入：root = []
输出：0
```

### 题解方法一： 递归

**`解题思路：`**

* 路径每到一个节点，有 3 种选择：1. 停在当前节点。2. 走到左子节点。3. 走到右子节点。
* 走到子节点，又面临这 3 种选择，递归适合处理这种规模不同的同一问题。
* 注意，不能走进一个分支又掉头回来走另一个分支，路径会重叠，不符合题目要求。


![截屏2022-06-13 下午2.21.13.png](https://upload-images.jianshu.io/upload_images/11846892-0d4880bdb75e8dd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`定义递归函数`**
* 对于一个父节点，它关心自己走入一个子树，从中捞取的最大收益，不关心具体怎么走。
* 定义dfs函数：返回当前子树能向父节点“提供”的最大路径和。即，一条从父节点延伸下来的路径，能在当前子树中捞取的最大收益。分三种情况：
1、路径停在当前子树的根节点，在当前子树的最大收益：root.val
2、走入左子树，在当前子树的最大收益：root.val + dfs(root.left)
3、走入右子树，在当前子树的最大收益：root.val + dfs(root.right)
* 这对应了前面所说的三种选择，最大收益取三者最大：root.val+max(0, dfs(root.left), dfs(root.right))
* **再次提醒**: 一条从父节点延伸下来的路径，不能走入左子树又掉头走右子树，不能两头收益。
* 当遍历到null节点时，null 子树提供不了收益，返回 0。
* **如果某个子树 dfs 结果为负**，走入它，收益不增反减，该子树就没用，需杜绝走入，像对待 null 一样让它返回 0（壮士断腕）。
![截屏2022-06-13 下午2.24.32.png](https://upload-images.jianshu.io/upload_images/11846892-a5a6af4b7c4c7c8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`子树中的内部路径要包含根节点`**

* 题意可知，最大路径和，是可能产生于其中一个子树中的，就好比下图左一。
* 所以每递归一个子树，都求一下当前子树内部的最大路径和，见下图右一的绿字，从中比较出最大的。
* **注意**: 一个子树内部的路径，要包含当前子树的根节点。如果不包含，那还算什么属于当前子树的路径，那就是当前子树的子树的内部路径了。
* 所以，一个子树内部的最大路径和 = 左子树提供的最大路径和 + 根节点值 + 右子树提供的最大路径和。即dfs(root.left)+root.val+dfs(root.right)

![截屏2022-06-13 下午2.26.19.png](https://upload-images.jianshu.io/upload_images/11846892-6f1f6bd11427aa93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

代码实现如下
```
const maxPathSum = (root) => {
    let maxSum = Number.MIN_SAFE_INTEGER; // 最大路径和
    const def = (root) => {
        if(!root) return 0 // 遍历到null节点，收益0
        const left = dfs(root.left);  // 左子树提供的最大路径和
        const right = dfr(root.right); // 右子树提供的最大路径和
        const innerMaxSum = left + root.val + right  // 当前子树内部的最大路径和
        maxSum = Math.max(maxSum, innerMaxSum);  // 挑战最大纪录
        const outputMaxSum = root.val + Math.max(0, left, right); // 当前子树对外提供的最大和

        // 如果对外提供的路径和为负，直接返回0，否则正常返回

        return outputMaxSum < 0 ？ 0:outputMaxSum
    }
    dfs(root)
    return maxSum
}

```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
https://leetcode.cn/problems/binary-tree-maximum-path-sum/solution/shou-hui-tu-jie-hen-you-ya-de-yi-dao-dfsti-by-hyj8/

