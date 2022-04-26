---
title: leeCode-114： 二叉树展开为链表： (前序遍历两种解法+ 寻找前驱节点的一种解法)-中等
date: 2022-04-25 16:58:45
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述：  给你二叉树的根结点 root ，请你将它展开为一个单链表：展开后的单链表应该同样使用 TreeNode ，其中 right 子指针指向链表中下一个结点，而左子指针始终为 null 。展开后的单链表应该与二叉树 先序遍历 顺序相同。

**示例1:**

![截屏2022-04-25 下午5.01.06.png](https://upload-images.jianshu.io/upload_images/11846892-cfd25d60bdcda93c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
输入：root = [1,2,5,3,4,null,6]
输出：[1,null,2,null,3,null,4,null,5,null,6]
```

**示例2:**

```
输入：root = []
输出：[]
```

**示例3:**

```
输入：root = [0]
输出：[0]
```

## 题解方法一：前序遍历和展开相继进行

**`解题思路`**
* 于当前节点，如果其左子节点不为空，则在其左子树中找到最右边的节点，作为前驱节点，将当前节点的右子节点赋给前驱节点的右子节点，然后将当前节点的左子节点赋给当前节点的右子节点，并将当前节点的左子节点设为空。对当前节点处理结束后，继续处理链表中的下一个节点，直到所有节点都处理结束。

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/solution/er-cha-shu-zhan-kai-wei-lian-biao-by-leetcode-solu/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

代码实现如下

```
var flatten = function(root) {
    const list = [];
    preorderTraversal(root, list);
    const size = list.length;
    for (let i = 1; i < size; i++) {
        const prev = list[i - 1], curr = list[i];
        prev.left = null;
        prev.right = curr;
    }
};

const preorderTraversal = (root, list) => {
    if (root != null) {
        list.push(root);
        preorderTraversal(root.left, list);
        preorderTraversal(root.right, list);
    }
}
```
迭代代码实现如下

```
var flatten = function(root) {
    const list = [];
    const stack = [];
    let node = root;
    while (node !== null || stack.length) {
        while (node !== null) {
            list.push(node);
            stack.push(node);
            node = node.left;
        }
        node = stack.pop();
        node = node.right;
    }
    const size = list.length;
    for (let i = 1; i < size; i++) {
        const prev = list[i - 1], curr = list[i];
        prev.left = null;
        prev.right = curr;
    }
};
```

## 题解方法二：前序遍历和展开同步进行

**`解题思路（没看懂可以看下面的图解）`**
* 展开为单链表的做法是，维护上一个访问的节点 prev，每次访问一个节点时，令当前访问的节点为 curr，将 prev 的左子节点设为 null 以及将 prev 的右子节点设为 curr，然后将 curr 赋值给 prev，进入下一个节点的访问，直到遍历结束。需要注意的是，初始时 prev 为 null，只有在 prev 不为 null 时才能对 prev 的左右子节点进行更新。


代码实现如下：
```
var flatten = function(root) {
    if (root === null) {
        return;
    }
    const stack = [];
    stack.push(root);
    let prev = null;
    while (stack.length) {
        const curr = stack.pop();
        if (prev !== null) {
            prev.left = null;
            prev.right = curr;
        }
        const left = curr.left, right = curr.right;
        if (right !== null) {
            stack.push(right);
        }
        if (left !== null) {
            stack.push(left);
        }
        prev = curr;
    }
};
```

我们以示例1为例，进行图解分析

```
输入：root = [1,2,5,3,4,null,6]
输出：[1,null,2,null,3,null,4,null,5,null,6]
```

执行代码第一步：
``` 
stack.push(root);
```
![截屏2022-04-25 下午5.19.30.png](https://upload-images.jianshu.io/upload_images/11846892-88b1bc8a393dc6ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时stack.length不为0 ，进入while循环: 第一次循环

执行代码第2步：
```
while (stack.length) { stack = [0]
        const curr = stack.pop(); // while第一次循环第一步：curr = 1, stack = []
        if (prev !== null) {
            prev.left = null;
            prev.right = curr;
        }
        const left = curr.left, right = curr.right; //while第一次循环第二步：left = 2, right = 5
        if (right !== null) { //while第一次循环第三步：stack = [5,2]
            stack.push(right);
        }
        if (left !== null) {
            stack.push(left);
        } 
        prev = curr; //while第一次循环第四步： prev = 1 
    }

```
while第一次循环第一步：

![截屏2022-04-25 下午5.21.29.png](https://upload-images.jianshu.io/upload_images/11846892-91b8baa5ddc317be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


while第一次循环第二步：![截屏2022-04-25 下午5.27.28.png](https://upload-images.jianshu.io/upload_images/11846892-6de9952f7769aa3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

while第一次循环第3步：![截屏2022-04-25 下午5.27.42.png](https://upload-images.jianshu.io/upload_images/11846892-66b24bab76dc3e3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

while第一次循环第4步：

![截屏2022-04-25 下午5.30.05.png](https://upload-images.jianshu.io/upload_images/11846892-1a5a68a69b075bc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行代码第3步：
```
while (stack.length) { stack = [5,2]
        const curr = stack.pop(); // while第二次循环第一步：curr = 2, stack = [5]
        if (prev !== null) { // while第二次循环第2步：prev = 1
            prev.left = null;
            prev.right = curr;
        }
        const left = curr.left, right = curr.right; //while第2次循环第3步：left = 3, right = 4
        if (right !== null) { //while第2次循环第四步：stack = [5,4，3]
            stack.push(right);
        }
        if (left !== null) {
            stack.push(left);
        } 
        prev = curr; //while第2次循环第五步： prev = 2
    }
```

while第2次循环第一步：

![截屏2022-04-25 下午5.53.38.png](https://upload-images.jianshu.io/upload_images/11846892-61f62e54e6a9b851.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




while第2次循环第二步：![截屏2022-04-25 下午5.34.40.png](https://upload-images.jianshu.io/upload_images/11846892-cf49bbf472100c05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


while第2次循环第3步：![截屏2022-04-25 下午5.35.36.png](https://upload-images.jianshu.io/upload_images/11846892-a8236ce2125e1de2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


while第2次循环第4步：

![截屏2022-04-25 下午5.36.35.png](https://upload-images.jianshu.io/upload_images/11846892-95a155041b650267.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


while第2次循环第5步：

![截屏2022-04-25 下午5.38.15.png](https://upload-images.jianshu.io/upload_images/11846892-804283a413f12461.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


执行代码第4步：
```
while (stack.length) { stack = [5,4，3]
        const curr = stack.pop(); // while第3次循环第一步：curr = 3, stack = [5， 4]
        if (prev !== null) { // while第3次循环第2步：prev = 2
            prev.left = null;
            prev.right = curr;
        }
        const left = curr.left, right = curr.right; //while第3次循环第3步：left = null, right = null
        if (right !== null) { 
            stack.push(right);
        }
        if (left !== null) {
            stack.push(left);
        } 
        prev = curr; //while第3次循环第四步： prev = 3
    }
```

while第3次循环第一步：

![截屏2022-04-25 下午5.43.24.png](https://upload-images.jianshu.io/upload_images/11846892-65a91f2ce7d0656e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


while第3次循环第二步：

![截屏2022-04-25 下午5.43.37.png](https://upload-images.jianshu.io/upload_images/11846892-697c50250c51bdb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


while第3次循环第3步：

![截屏2022-04-25 下午5.44.44.png](https://upload-images.jianshu.io/upload_images/11846892-cf397ecb3c29bc0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




while第3次循环第4步：

![截屏2022-04-25 下午5.45.40.png](https://upload-images.jianshu.io/upload_images/11846892-ea6e32394b1dd5e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




执行代码第5步：
```
while (stack.length) { stack = [5,4]
        const curr = stack.pop(); // while第4次循环第一步：curr = 4, stack = [5]
        if (prev !== null) { // while第4次循环第2步：prev = 3
            prev.left = null;
            prev.right = curr;
        }
        const left = curr.left, right = curr.right; //while第4次循环第3步：left = null, right = null
        if (right !== null) { 
            stack.push(right);
        }
        if (left !== null) {
            stack.push(left);
        } 
        prev = curr; //while第4次循环第四步： prev = 4
    }
```

while第4次循环第一步：

![截屏2022-04-25 下午5.48.09.png](https://upload-images.jianshu.io/upload_images/11846892-1515f18459d0fdec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



while第4次循环第二步：
![截屏2022-04-25 下午5.57.32.png](https://upload-images.jianshu.io/upload_images/11846892-2e4992a6ecded746.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

while第4次循环第3步：

![截屏2022-04-25 下午5.57.56.png](https://upload-images.jianshu.io/upload_images/11846892-29eef28a42628e43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





while第4次循环第4步：

![截屏2022-04-25 下午5.58.25.png](https://upload-images.jianshu.io/upload_images/11846892-c504e745a0ce0e1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



执行代码第6步：
```
while (stack.length) { stack = [5]
        const curr = stack.pop(); // while第5次循环第一步：curr = 5, stack = []
        if (prev !== null) { // while第5次循环第2步：prev = 4
            prev.left = null;
            prev.right = curr;
        }
        const left = curr.left, right = curr.right; //while第5次循环第3步：left = null, right = 6 stack = [6]
        if (right !== null) { 
            stack.push(right);
        }
        if (left !== null) {
            stack.push(left);
        } 
        prev = curr; //while第5次循环第四步： prev = 5
    }
```

while第5次循环第一步：

![截屏2022-04-25 下午6.06.00.png](https://upload-images.jianshu.io/upload_images/11846892-ee93bd5b55fb35c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




while第5次循环第二步：
![截屏2022-04-25 下午6.07.12.png](https://upload-images.jianshu.io/upload_images/11846892-ecbde1786c191e9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)






while第5次循环第3步：

![截屏2022-04-25 下午6.08.06.png](https://upload-images.jianshu.io/upload_images/11846892-a4f5f399adee86dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



while第5次循环第4步：

![截屏2022-04-25 下午6.08.35.png](https://upload-images.jianshu.io/upload_images/11846892-0be38d180e47873f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



执行代码第7步：
```
while (stack.length) { stack = [6]
        const curr = stack.pop(); // while第6次循环第一步：curr = 6, stack = []
        if (prev !== null) { // while第6次循环第2步：prev = 5
            prev.left = null;
            prev.right = curr;
        }
        const left = curr.left, right = curr.right; //while第6次循环第3步：left = null, right = null stack = []
        if (right !== null) { 
            stack.push(right);
        }
        if (left !== null) {
            stack.push(left);
        } 
        prev = curr; //while第6次循环第四步： prev = 6
    }
```

while第6次循环第一步：

![截屏2022-04-25 下午6.10.18.png](https://upload-images.jianshu.io/upload_images/11846892-3be80377bdf3d6d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




while第6次循环第二步：
![截屏2022-04-25 下午6.11.12.png](https://upload-images.jianshu.io/upload_images/11846892-af4b5c965e868b43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





while第6次循环第3步：

![截屏2022-04-25 下午6.11.12.png](https://upload-images.jianshu.io/upload_images/11846892-af4b5c965e868b43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



while第6次循环第4步：

![截屏2022-04-25 下午6.12.51.png](https://upload-images.jianshu.io/upload_images/11846892-a74965dc5a023989.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时stack.length 为0 跳出循环，函数执行完毕！！


## 题解方法三：寻找前驱节点的方法

**`解题思路`**
* 对于当前节点，如果其左子节点不为空，则在其左子树中找到最右边的节点，作为前驱节点，将当前节点的右子节点赋给前驱节点的右子节点，然后将当前节点的左子节点赋给当前节点的右子节点，并将当前节点的左子节点设为空。对当前节点处理结束后，继续处理链表中的下一个节点，直到所有节点都处理结束。

前驱节点代码实现如下：
```
var flatten = function(root) {
    let curr = root;
    while(curr !== null) {
        if(curr.left !==null) {
            const next = curr.left;
            let predecessor = next;
            while(predecessor.right!==null){
                predecessor = predecessor.right;
            }
            predecessor.right = curr.right;
            curr.left = null;
            curr.right = next;
        }
        curr = curr.right
    }
}
```



我们以示例1为例，进行图解分析

```
输入：root = [1,2,5,3,4,null,6]
输出：[1,null,2,null,3,null,4,null,5,null,6]
```

执行代码第一步：
``` 
let curr = root;
```

![截屏2022-04-26 上午10.06.40.png](https://upload-images.jianshu.io/upload_images/11846892-a31a107b4fa98fb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


进入第1次while循环☝️：

```
while(curr !== null) { 
        if(curr.left !==null) {
            const next = curr.left; //第1次while循环第1步: next = predecessor = 2
            let predecessor = next;
            while(predecessor.right!==null){ //第1次while循环第2步: next = 2, predecessor = 4
                predecessor = predecessor.right;
            }
            predecessor.right = curr.right; //第1次while循环第3步: predecessor.right = 5
            curr.left = null;
            curr.right = next;
        }
        curr = curr.right
    }

```


第1次while循环第1步: next = predecessor = 2

![截屏2022-04-26 上午10.08.57.png](https://upload-images.jianshu.io/upload_images/11846892-aa1327ebacbf5d74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第1次while循环第2步: next = 2, predecessor = 4
 ![截屏2022-04-26 上午10.09.59.png](https://upload-images.jianshu.io/upload_images/11846892-e456e81b0887af8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第1次while循环第3步: predecessor.right = 5

![截屏2022-04-26 上午10.11.57.png](https://upload-images.jianshu.io/upload_images/11846892-c3aef6805e645e03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
