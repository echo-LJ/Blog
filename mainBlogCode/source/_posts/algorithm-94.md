---
title: leeCode-94： 二叉树的中序遍历： (迭代、递归、Morris遍历三种解法)-简单
date: 2022-04-27 16:21:52
tags: 算法
---

<meta name="referrer" content="no-referrer"/>

## 题目描述： 给定一个二叉树的根节点 root ，返回 它的 中序 遍历 。

**示例1:**

![屏幕快照 2022-04-24 下午4.27.55.png](https://upload-images.jianshu.io/upload_images/11846892-ecf6dc20ad7a9e22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,null,2,3]
输出：[1,3,2]
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

解题思路：二叉树的前序遍历：按照访问左子树——根节点——右子树的方式遍历这棵树，而在访问左子树或者右子树的时候，我们按照同样的方式遍历，直到遍历完整棵树。因此整个遍历过程天然具有递归的性质，我们可以直接用递归函数来模拟这一过程。

代码实现如下：
```
var inorderTraversal = (root) => {
  let arr = []
  inorder(root, arr);
  return arr
}
var inorder = (root, arr) => {
  if (root === null) return
  inorder(root.left, arr)
  arr.push(root.val);
  inorder(root.right, arr)
}
```

## 题解方法二：迭代

解题思路：我们也可以用迭代的方式实现方法一的递归函数，两种方式是等价的，区别在于递归的时候隐式地维护了一个栈，而我们在迭代的时候需要显式地将这个栈模拟出来，其余的实现与细节都相同。

代码实现如下：
```
var inorderTraversal = function(root) {
    const res = [];
    const stk = [];
    while (root || stk.length) {
        while (root) {
            stk.push(root);
            root = root.left;
        }
        root = stk.pop();
        res.push(root.val);
        root = root.right;
    }
    return res;
};
```

我们以如下示例为例，进行图解分析：

![截屏2022-04-27 下午4.43.33.png](https://upload-images.jianshu.io/upload_images/11846892-ad0ec612334b2940.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


rooth不为null ，进入第一次while循环
```
while (root || stk.length) {
        while (root) { //第一次while循环第1步执行完毕 stack = [1,2,3] root = null
            stk.push(root);
            root = root.left;
        } 
        root = stk.pop(); //第一次while循环第2步 stack = [1,2] root = 3， res = [3],
        res.push(root.val);
        root = root.right; //第一次while循环第3步 root = null
    }
``` 
执行第一次循环：

第一步：
第二步：
第三步：
此时stack = [1,2] ，进入第二次while循环
```
while (root || stk.length) { // root = null stack = [1,2]
        while (root) { // root = null
            stk.push(root);
            root = root.left;
        } 
        root = stk.pop(); //第2次while循环第1步 stack = [1] root = 2， res = [3,2],
        res.push(root.val);
        root = root.right; //第2次while循环第2步 root = 4
    }
``` 
执行第2次循环：

第一步：
第二步：
第三步：

此时stack = [1] root = 4 ，进入第3次while循环
```
while (root || stk.length) { // root = 4 stack = [1]
        while (root) { // 第2次while循环第1步 stack = [1,4]
            stk.push(root);
            root = root.left;
        } 
        root = stk.pop(); //第3次while循环第2步 stack = [1] root = 4， res = [3,2, 4],
        res.push(root.val);
        root = root.right; //第3次while循环第3步 root = null
    }
``` 
执行第3次循环：

第一步：
第二步：
第三步：

此时stack = [1] root = null ，进入第4次while循环
```
while (root || stk.length) { // root = null stack = [1]
        while (root) { 
            stk.push(root);
            root = root.left;
        } 
        root = stk.pop(); //第4次while循环第1步 stack = [] root = 1， res = [3,2, 4， 1],
        res.push(root.val);
        root = root.right; //第4次while循环第2步 root = 5
    }
``` 
执行第4次循环：

第一步：
第二步：

此时stack = [] root = 5 ，进入第5次while循环
```
while (root || stk.length) { // root = 5 stack = []
        while (root) {  //第5次while循环第1步 stack = [5] root = null
            stk.push(root);
            root = root.left;
        } 
        root = stk.pop(); //第5次while循环第2步 stack = [] root = 5， res = [3,2, 4， 1, 5],
        res.push(root.val);
        root = root.right; //第5次while循环第3步 root = null
    }
``` 
执行第5次循环：

第一步：
第二步：
第三步：

此时stack = [] root = null结束循环，执行完毕！！

## 题解方法三：Morris遍历 !!没看懂

解题思路：

如果 root 无左孩子，先将 root 的值加入答案数组，再访问 root 的右孩子，即 root = root.right。
如果 root 有左孩子，则找到 root 左子树上最右的节点（即左子树中序遍历的最后一个节点，root 在中序遍历中的前驱节点），我们记为predecessor。根据predecessor 的右孩子是否为空，进行如下操作。
如果 predecessor 的右孩子为空，则将其右孩子指向 root，然后访问 root 的左孩子，即 root=root.left。
如果 predecessor 的右孩子不为空，则此时其右孩子指向 root，说明我们已经遍历完 root 的左子树，我们将 predecessor 的右孩子置空，将 root 的值加入答案数组，然后访问 root 的右孩子，即 root = root.right。
重复上述操作，直至访问完整棵树。

代码实现如下：

```
var inorderTraversal = function(root) {
    const res = [];
    let predecessor = null;

    while (root) {
        if (root.left) {
            // predecessor 节点就是当前 root 节点向左走一步，然后一直向右走至无法走为止
            predecessor = root.left;
            while (predecessor.right && predecessor.right !== root) {
                predecessor = predecessor.right;
            }

            // 让 predecessor 的右指针指向 root，继续遍历左子树
            if (!predecessor.right) {
                predecessor.right = root;
                root = root.left;
            }
            // 说明左子树已经访问完了，我们需要断开链接
            else {
                res.push(root.val);
                predecessor.right = null;
                root = root.right;
            }
        }
        // 如果没有左孩子，则直接访问右孩子
        else {
            res.push(root.val);
            root = root.right;
        }
    }

    return res;
};
}
var inorderTraversal = function(root) {
    const res = [];
    let predecessor = null;

    while (root) {
        if (root.left) {
            // predecessor 节点就是当前 root 节点向左走一步，然后一直向右走至无法走为止
            predecessor = root.left;
            while (predecessor.right && predecessor.right !== root) {
                predecessor = predecessor.right;
            }

            // 让 predecessor 的右指针指向 root，继续遍历左子树
            if (!predecessor.right) {
                predecessor.right = root;
                root = root.left;
            }
            // 说明左子树已经访问完了，我们需要断开链接
            else {
                res.push(root.val);
                predecessor.right = null;
                root = root.right;
            }
        }
        // 如果没有左孩子，则直接访问右孩子
        else {
            res.push(root.val);
            root = root.right;
        }
    }

    return res;
};

}

```


参考地址：

* [二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)
* https://leetcode-cn.com/problems/binary-tree-preorder-traversal/solution/bao-ni-hui-de-dui-ge-lei-ti-jie-zhong-ge-mlgr/



