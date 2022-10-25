---
title: leeCode-538： 把二叉搜索树转换为累加树： (反序中序遍历)-中等
date: 2022-06-07 16:22:15
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

## 题目描述： 给出二叉 搜索 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 node 的新值等于原树中大于或等于 node.val 的值之和 。

**`提醒一下: 二叉搜索树满足下列约束条件`**
* 节点的左子树仅包含键 小于 节点键的节点。
* 节点的右子树仅包含键 大于 节点键的节点。
* 左右子树也必须是二叉搜索树。

**示例1:**

![demo1.png](https://upload-images.jianshu.io/upload_images/11846892-d7aa9f950e857af9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
输入：[4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
输出：[30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

**示例2:**

```
输入：root = [0,null,1]
输出：[1,null,1]
```

**示例3:**

```
输入：root = [1,0,2]
输出：[3,3,2]
```
**示例4:**
```
输入：root = [3,2,4,1]
输出：[7,9,4,10]
```

**示例5:**

![屏幕快照 2022-04-24 下午4.29.39.png](https://upload-images.jianshu.io/upload_images/11846892-317cb88b31fa5df5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,null,2]
输出：[1,2]
```


**`解题分析`**：二叉搜索树是一棵空树，或者是具有下列性质的二叉树：

* 若它的左子树不空，则左子树上所有节点的值均小于它的根节点的值；

* 若它的右子树不空，则右子树上所有节点的值均大于它的根节点的值；

* 它的左、右子树也分别为二叉搜索树。

* 由这样的性质我们可以发现，二叉搜索树的中序遍历是一个单调递增的有序序列。如果我们反序地中序遍历该二叉搜索树，即可得到一个单调递减的有序序列。


## 题解方法一：反序中序遍历

**`解题思路:`** 我们只需要反序中序遍历该二叉搜索树，记录过程中的节点值之和，并不断更新当前遍历到的节点的节点值，即可得到题目要求的累加树。

代码实现如下：
```
var convertBST = function(root) {
    var sum = 0; // 执行代码第一步
    var inorder = (root) => {
        if(!root) return 
        inorder(root.right)
        sum += root.val
        root.val = sum;
        inorder(root.left)
    }
    inorder(root) // 执行代码第一步
    return root
};
```

我们以示例1为例，进行图解分析
```
输入：root = [3,2,4,1]
输出：[7,9,4,10]
```


执行代码第一步：
``` 
var convertBST = function(root) {
    var sum = 0; // 执行代码第一步
    var inorder = (root) => {
        if(!root) return 
        inorder(root.right)
        sum += root.val
        root.val = sum;
        inorder(root.left)
    }
    inorder(root) // 执行代码第一步
    return root
};
```
![step1.png](https://upload-images.jianshu.io/upload_images/11846892-08c1f0f313574741.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
执行代码第二步：
```
inorder(root.right)
```

![截屏2022-06-07 下午5.08.05.png](https://upload-images.jianshu.io/upload_images/11846892-e6bb6dce99403d3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第三步：
```
// 第二步中的inorder(root.right)
var inorder = (root) => { root = 4
    if(!root) return 
    inorder(root.right)
    sum += root.val  // 执行代码第三步： sum = 4
    root.val = sum;
    inorder(root.left)
}
```
![截屏2022-06-07 下午5.17.06.png](https://upload-images.jianshu.io/upload_images/11846892-d438442a578bd45c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


执行代码第四、五步：
```
// 第三步中的(root.left) root = 4 root.left = null
var inorder = (root) => { 
    if(!root) return  // 执行代码第五步 root === null， 跳出方法即跳出第二步中进入(root.right) 这个方法(root.right = 4)
    inorder(root.right)
    sum += root.val  
    root.val = sum;
    inorder(root.left) // 执行代码第四步root.left = null
}
```
![截屏2022-06-07 下午5.26.52.png](https://upload-images.jianshu.io/upload_images/11846892-12708c99dd1a2b94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第六步：

```
// 进入第二步中的方法 root = 3 sum = 4

 sum += root.val
 root.val = sum;

```
![截屏2022-06-07 下午5.27.39.png](https://upload-images.jianshu.io/upload_images/11846892-247d62b277c870b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第七步：

```
// 进入第二步中的方法 root = 7 sum = 7

 inorder(root.left)  // root.left = 2

```
![截屏2022-06-07 下午5.29.02.png](https://upload-images.jianshu.io/upload_images/11846892-8042920d416924f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第八步：

```
// 第七步中的(root.left) root = 2 root.right = null  sum = 7
var inorder = (root) => { 
    if(!root) return  
    inorder(root.right) // 执行代码第八步： root.right = null 跳出方法，sum不变
    sum += root.val   // 执行代码第八步： sum = 9= 7+2
    root.val = sum;  // 执行代码第八步： root = 9
    inorder(root.left) 
}
```
![截屏2022-06-07 下午5.29.02.png](https://upload-images.jianshu.io/upload_images/11846892-8042920d416924f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第九步：

```
// 第八步中的(root.left) root = 1 root.right = null  sum = 9
var inorder = (root) => { 
    if(!root) return  
    inorder(root.right) // 执行代码第9步： root.right = null 跳出方法，sum不变
    sum += root.val   // 执行代码第9步： sum = 10 = 9 +1
    root.val = sum;  // 执行代码第9步： root = 10 
    inorder(root.left) // 执行代码第9步 root.left = null 跳出方法
}
```
![截屏2022-06-07 下午5.33.50.png](https://upload-images.jianshu.io/upload_images/11846892-c70acfd89dc3950e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行完毕！

## 题解方法二：Morris遍历

```
var convertBST = function(root) {
    let sum = 0
    let node = root
    while(node) {
        if(node.right == null) {
            sum+= node.val;
            node.val = sum
            node = node.left
        } else {
            let succ = getSuccessor(node)
            if(succ.left) {
                succ.left = null;
                sum += node.val
                node.val = sum
                node =node.left
            }else {
                succ.left = node;
                node = node.right
            }
        }
    }
    
    return root
};

var getSuccessor = (node) => {
    let succ = node.right
    while(succ.left && succ.left != node) {
        succ = succ.left
    }
    return succ
}

```

我们以示例1为例，进行图解分析
```
输入：root = [3,2,4,1]
输出：[7,9,4,10]
```

执行代码第1步：
``` 
var convertBST = function(root) { // 执行代码第1步: root = 3
    let sum = 0
    let node = root
    while(node) {
        if(node.right == null) {
            sum+= node.val;
            node.val = sum
            node = node.left
        } else {
            let succ = getSuccessor(node) // 执行代码第1步: node = 3
            if(succ.left) {
                succ.left = null;
                sum += node.val
                node.val = sum
                node =node.left
            }else {
                succ.left = node;
                node = node.right
            }
        }
    }
    
    return root
};
```
![截屏2022-06-08 下午2.51.52.png](https://upload-images.jianshu.io/upload_images/11846892-c4cc7a2661e37e6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



执行代码第2步：
* 获取到当前节点的右子树的最左侧节点
```
var getSuccessor = (node) => { // 执行代码第2步: 3
    let succ = node.right 
    while(succ.left && succ.left != node) {
        succ = succ.left
    }
    return succ // 执行代码第2步: succ = 4
}

```
![截屏2022-06-08 下午2.52.12.png](https://upload-images.jianshu.io/upload_images/11846892-9271a7e396aed83f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第3步：
* 回到第一步中的`let succ = getSuccessor(node)`的这行代码，在第2步中我们已经获取到succ = 4

```
var convertBST = function(root) { // 执行代码第1步: root = 3
    let sum = 0
    let node = root
    while(node) {
        if(node.right == null) {
            sum+= node.val;
            node.val = sum
            node = node.left
        } else {
            let succ = getSuccessor(node) // 执行代码第2步: node = 3 succ = 4
            if(succ.left) {
                succ.left = null;
                sum += node.val
                node.val = sum
                node =node.left
            }else {  // 执行代码第3步: node = 3 succ = 4
                succ.left = node;  // 执行代码第3步: succ.left = 3
                node = node.right // 执行代码第3步: node = 4
            }
        }
    }
    
    return root
};
```
![截屏2022-06-08 下午2.53.59.png](https://upload-images.jianshu.io/upload_images/11846892-eddc2aad4ad40ef8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行到目前为止，可以看到，我们已经把root变成只有左子树，那么接下来可以逐行进行累加即可!


执行代码第4步：
* 重新逐次进入while循环， 因为没有右子树，逐次遍历左节点，向下累加，即可得到新的累加树
```
var convertBST = function(root) {
    let sum = 0
    let node = root
    while(node) { // 执行代码第4步: node = 4
        if(node.right == null) {
            sum+= node.val; // 执行代码第4步: sum = 4
            node.val = sum  
            node = node.left //  执行代码第4步: node = 3
        } else {
            let succ = getSuccessor(node) 
            if(succ.left) {
                succ.left = null;
                sum += node.val
                node.val = sum
                node =node.left
            }else {  
                succ.left = node;  
                node = node.right 
            }
        }
    }
    
    return root
};
```
![截屏2022-06-08 下午2.59.36.png](https://upload-images.jianshu.io/upload_images/11846892-b4d6219f932844f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-06-08 下午2.59.58.png](https://upload-images.jianshu.io/upload_images/11846892-3a52d15361f47c28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


执行代码第5步：
![截屏2022-06-08 下午3.00.32.png](https://upload-images.jianshu.io/upload_images/11846892-cb21b90a583ef7ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-06-08 下午3.01.34.png](https://upload-images.jianshu.io/upload_images/11846892-00d9349c5d72db9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


执行代码第6步：
![截屏2022-06-08 下午3.02.31.png](https://upload-images.jianshu.io/upload_images/11846892-0b858b1842fb5a8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-06-08 下午3.02.43.png](https://upload-images.jianshu.io/upload_images/11846892-77546d21818cef5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第7步：
![截屏2022-06-08 下午3.03.53.png](https://upload-images.jianshu.io/upload_images/11846892-7cc2e54e53489fe1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![截屏2022-06-08 下午3.04.29.png](https://upload-images.jianshu.io/upload_images/11846892-dae16723c3d569cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时node = null跳出while循环即可，返回root！
执行完毕！！
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:
https://leetcode.cn/problems/convert-bst-to-greater-tree/solution/ba-er-cha-sou-suo-shu-zhuan-huan-wei-lei-jia-sh-14/