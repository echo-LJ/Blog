---
title: leeCode-437： 路径总和III-中等
date: 2022-06-17 13:40:20
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述： 给定一个二叉树的根节点 root ，和一个整数 targetSum ，求该二叉树里节点值之和等于 targetSum 的 路径 的数目。 路径 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。


**示例1:**

![截屏2022-06-17 下午1.45.00.png](https://upload-images.jianshu.io/upload_images/11846892-9e8f56d9a785e81f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
输出：3
解释：和等于 8 的路径有 3 条，如图所示。

```
**示例2:**

```
输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
输出：3

```

## 题解方法一：前缀和+递归+回溯

`前缀和的递归回溯思路:`

* 从当前节点反推到根节点(反推比较好理解，正向其实也只有一条)，有且仅有一条路径，因为这是一棵树
* 如果此前有和为currSum-target,而当前的和又为currSum,两者的差就肯定为target了
* 所以前缀和对于当前路径来说是唯一的，当前记录的前缀和，在回溯结束，回到本层时去除，保证其不影响其他分支的结果


代码实现如下： 
```

var pathSum = function(root, targetSum) {
    const prefix = new Map();
    prefix.set(0, 1);
    return dfs(root, prefix, 0, targetSum);
}

var dfs = (root, prefix, curr, targetSum) => {
    
    // 1.递归终止条件
    if (root == null) {
        return 0;
    }

    // 2.本层要做的事情

    let ret = 0;
    
    curr += root.val; // 当前路径上的和

    // 看看root到当前节点这条路上是否存在节点前缀和 + target = currSum的路径
    // 当前节点root节点反推，有且仅有一条路径，如果此前有和为currSum-target,而当前的和又为currSum,两者的差就肯定为target了
    // currSum-target相当于找路径的起点，起点的sum+target=currSum，当前点到起点的距离就是target

    ret = prefix.get(curr - targetSum) || 0;
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 更新路径上当前节点前缀和的个数

        // 3.进入下一层
    ret += dfs(root.left, prefix, curr, targetSum);
    ret += dfs(root.right, prefix, curr, targetSum);

    // 4.回到本层，恢复状态，去除当前节点的前缀和数量
    prefix.set(curr, (prefix.get(curr) || 0) - 1);
    return ret;
}

```

我们以示例1为例，进行图解分析
![截屏2022-06-17 下午2.03.28.png](https://upload-images.jianshu.io/upload_images/11846892-6340df48bc24e42c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`执行代码第1步`**
```
var pathSum = function(root, targetSum) {
    const prefix = new Map();
    prefix.set(0, 1);
    return dfs(root, prefix, 0, targetSum);
}

```
**`执行代码第2步`**

```

var dfs = (root, prefix, curr, targetSum) => {
    if (root == null) {
        return 0;
    }
    let ret = 0; // 执行代码第2步
    
    curr += root.val; // 执行代码第2步 curr = 0 + 10 = 10

    ret = prefix.get(curr - targetSum) || 0; // 执行代码第2步 curr - targetSum = 10 - 8 = 2  ret = 0
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 执行代码第2步

    ret += dfs(root.left, prefix, curr, targetSum); // 执行代码第3步
    ret += dfs(root.right, prefix, curr, targetSum);

    prefix.set(curr, (prefix.get(curr) || 0) - 1);
    return ret;
}
```
![截屏2022-06-17 下午2.43.58.png](https://upload-images.jianshu.io/upload_images/11846892-b055ed487ade8450.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`执行代码第3步`**
```

var dfs = (root, prefix, curr, targetSum) => {
    if (root == null) {
        return 0;
    }
    let ret = 0; // 执行代码第3步
    
    curr += root.val; // 执行代码第3步 curr = 10 + 5 = 15

    ret = prefix.get(curr - targetSum) || 0; // 执行代码第3步 curr - targetSum = 15 - 8 = 7  ret = 0
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 执行代码第3步

    ret += dfs(root.left, prefix, curr, targetSum); // 执行代码第4步 ret = 1
    ret += dfs(root.right, prefix, curr, targetSum); // 执行代码第10步 ret = 1+ 1 =2

    prefix.set(curr, (prefix.get(curr) || 0) - 1);
    return ret;
}
```
![截屏2022-06-17 下午2.49.28.png](https://upload-images.jianshu.io/upload_images/11846892-7c0ba8e6d082991c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`执行代码第4步`**
```

var dfs = (root, prefix, curr, targetSum) => {
    if (root == null) {
        return 0;
    }
    let ret = 0; // 执行代码第4步
    
    curr += root.val; // 执行代码第4步 curr = 15 + 3 = 18

    ret = prefix.get(curr - targetSum) || 0; // 执行代码第4步 curr - targetSum = 18 - 8 = 19  ret = 1
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 执行代码第4步

    ret += dfs(root.left, prefix, curr, targetSum); // 执行代码第5步
    ret += dfs(root.right, prefix, curr, targetSum); // root.right = null 执行返回

    prefix.set(curr, (prefix.get(curr) || 0) - 1); // 执行代码第9步
    return ret;
}
```
![截屏2022-06-17 下午3.03.38.png](https://upload-images.jianshu.io/upload_images/11846892-8165fb1e20378429.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`执行代码第5步`**
```

var dfs = (root, prefix, curr, targetSum) => {
    if (root == null) {
        return 0;
    }
    let ret = 0; // 执行代码第5步
    
    curr += root.val; // 执行代码第5步 curr = 18 + 3 = 21

    ret = prefix.get(curr - targetSum) || 0; // 执行代码第5步 curr - targetSum = 21 - 8 = 13  ret = 0
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 执行代码第5步

    ret += dfs(root.left, prefix, curr, targetSum); // 执行代码第6步 root.left = null 直接返回 0 
    ret += dfs(root.right, prefix, curr, targetSum); // 执行代码第7步

    prefix.set(curr, (prefix.get(curr) || 0) - 1); // 执行代码第8步
    return ret;
}
```
![截屏2022-06-17 下午3.11.42.png](https://upload-images.jianshu.io/upload_images/11846892-8293412406cd9543.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`执行代码第6步 root.left = null 直接返回 0 `**

**`执行代码第7步`**
```

var dfs = (root, prefix, curr, targetSum) => {
    if (root == null) {
        return 0;
    }
    let ret = 0; // 执行代码第7步
    
    curr += root.val; // 执行代码第7步 curr = 21 - 3 = 19

    ret = prefix.get(curr - targetSum) || 0; // 执行代码第7步 curr - targetSum = 19 - 8 = 11  ret = 0
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 执行代码第7.1步

    ret += dfs(root.left, prefix, curr, targetSum); 
    ret += dfs(root.right, prefix, curr, targetSum); 

    prefix.set(curr, (prefix.get(curr) || 0) - 1); // 执行代码第7.2步
    return ret;
}
```
![截屏2022-06-17 下午3.13.35.png](https://upload-images.jianshu.io/upload_images/11846892-a9814d8925596b2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-06-17 下午3.15.41.png](https://upload-images.jianshu.io/upload_images/11846892-56a6d92db3db3d08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`执行代码第8步`**

第8步是在第5步方法中执行的，

![截屏2022-06-17 下午3.27.57.png](https://upload-images.jianshu.io/upload_images/11846892-591c0045c7f2b247.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`执行代码第9步`**

第9步是在第4步方法中执行的，

![截屏2022-06-17 下午3.31.48.png](https://upload-images.jianshu.io/upload_images/11846892-44c4966af5e02f67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`执行代码第10步`**

第10步是在第3步方法中执行的，

```
var dfs = (root, prefix, curr, targetSum) => {
    if (root == null) {
        return 0;
    }
    let ret = 0; // 执行代码第10步
    
    curr += root.val; // 执行代码第10步 curr = 15 + 2 = 17

    ret = prefix.get(curr - targetSum) || 0; // 执行代码第10步 curr - targetSum = 17 - 8 = 9  ret = 0
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 执行代码第10步

    ret += dfs(root.left, prefix, curr, targetSum); // 执行代码第10步 root.left = null 直接返回 0 
    ret += dfs(root.right, prefix, curr, targetSum); // 执行代码第11步

    prefix.set(curr, (prefix.get(curr) || 0) - 1); 
    return ret; // 第11步 ret = 1
}
```
![截屏2022-06-17 下午3.44.08.png](https://upload-images.jianshu.io/upload_images/11846892-d48f1b82d32038d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`执行代码第11步`**

```
var dfs = (root, prefix, curr, targetSum) => {
    if (root == null) {
        return 0;
    }
    let ret = 0; // 执行代码第11步
    
    curr += root.val; // 执行代码第11步 curr = 17 + 1 = 18

    ret = prefix.get(curr - targetSum) || 0; // 执行代码第11步 curr - targetSum = 18 - 8 = 10  ret = 1
    prefix.set(curr, (prefix.get(curr) || 0) + 1); // 执行代码第11步

    ret += dfs(root.left, prefix, curr, targetSum); // 执行代码第11步 root.left = null 直接返回 0 
    ret += dfs(root.right, prefix, curr, targetSum); // 执行代码第11步 root.left = null 直接返回 0 

    prefix.set(curr, (prefix.get(curr) || 0) - 1);  // 执行代码第11步
    return ret; // 执行代码第11步 ret = 1
}
```
![截屏2022-06-17 下午3.48.06.png](https://upload-images.jianshu.io/upload_images/11846892-885f85a2c85cf4bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


执行完第10步之后，返回到第3步中 ret = 2

右子树同理，此处省略相关图解，执行之后 ret = 3

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://github.com/sisterAn/JavaScript-Algorithms/issues/41
https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by--22/












