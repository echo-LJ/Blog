---
title: leeCode-297： 二叉树的序列化与反序列化 (遍历、递归两种解法)-困难
date: 2022-06-14 16:10:19
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述： 序列化是将一个数据结构或者对象转换为连续的比特位的操作，进而可以将转换后的数据存储在一个文件或者内存中，同时也可以通过网络传输到另一个计算机环境，采取相反方式重构得到原数据。请设计一个算法来实现二叉树的序列化与反序列化。这里不限定你的序列 / 反序列化算法执行逻辑，你只需要保证一个二叉树可以被序列化为一个字符串并且将这个字符串反序列化为原始的树结构。

**示例1:**

![截屏2022-06-14 下午2.01.53.png](https://upload-images.jianshu.io/upload_images/11846892-89c61fd19ec7a6f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：root = [1,2,3,null,null,4,5]
输出：[1,2,3,null,null,4,5]

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


```
输入：root = [1,2]
输出：[1,2]

```

## 题解方法一：递归DFS

`解题思路:`
* 递归遍历一棵树，重点关注当前节点，它的子树的遍历交给递归完成：serialize函数，请帮我分别序列化我的左右子树，我等你返回的结果，再拼接一下。”
* 选择前序遍历，是因为 根|左|右根∣左∣右 的打印顺序，在反序列化时更容易定位出根节点的值。
* 遇到 null 节点也要翻译成特定符号，反序列化时才知道这里是 null。



![截屏2022-06-14 下午2.05.55.png](https://upload-images.jianshu.io/upload_images/11846892-c695a86b9cb03dda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

序列化代码实现如下： 

```
const serialize = (root) => {
  if (root == null) {                  // 遍历到 null 节点
    return 'X';
  }
  const left = serialize(root.left);   // 左子树的序列化结果
  const right = serialize(root.right); // 右子树的序列化结果
  return root.val + ',' + left + ','+ right; // 按  根,左,右  拼接字符串
};
```

`反序列化实现方式:` 
* 前序遍历的序列化字符串，就像下图右一：
![截屏2022-06-14 下午2.08.13.png](https://upload-images.jianshu.io/upload_images/11846892-542fae2d275e1282.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 定义函数 buildTree 用于还原二叉树，传入由序列化字符串转成的 list 数组。
* 逐个 pop 出 list 的首项，构建当前子树的根节点，顺着 list，构建顺序是根节点，左子树，右子树。
    1、如果弹出的字符为 "X"，则返回 null 节点。
    2、如果弹出的字符是数值，则创建root节点，并递归构建root的左右子树，最后返回root。
![截屏2022-06-14 下午2.09.50.png](https://upload-images.jianshu.io/upload_images/11846892-4c52d349cd96979b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

反序列化代码实现如下： 

```
const deserialize = (data) => {
  const list = data.split(',');   // split成数组

  const buildTree = (list) => {   // 基于list构建当前子树
    const rootVal = list.shift(); // 弹出首项，获取它的“数据”
    if (rootVal == "X") {         // 是X，返回null节点
      return null;
    }
    const root = new TreeNode(rootVal); // 不是X，则创建节点
    root.left = buildTree(list);        // 递归构建左子树
    root.right = buildTree(list);       // 递归构建右子树
    return root;                        // 返回当前构建好的root
  };

  return buildTree(list); // 构建的入口
};
```


## 题解方法二：遍历BFS

* 维护一个队列，初始让根节点入列，考察出列节点：
    1、如果出列的节点是 null，将符号 'X' 推入 res 数组。
    2、如果出列的节点是数值，将节点值推入数组 res，并将它的左右子节点入列。
* 入列、出列…直到队列为空，就遍历完所有节点，res构建完毕，转成字符串就好。



序列化代码实现如下： 

```
const serialize = (root) => {
  const queue = [root];
  let res = [];
  while (queue.length) {
    const node = queue.shift(); // 考察出列的节点
    if (node) {                 // 是真实节点，带出子节点入列
      res.push(node.val);       // 节点值推入res
      queue.push(node.left);    // 子节点入列，不管是不是null节点都入列
      queue.push(node.right);    
    } else {                    // 是null节点，没有子节点入列
      res.push('X');            // X 推入res
    }
  }
  return res.join(',');  // 转成字符串
}
```

`反序列化实现方式:` 

* 下图是BFS得到的序列化字符串，和DFS得到的不同，它是一层层的。除了第一个是根节点的值，其他节点值都是成对的，对应左右子节点。
![截屏2022-06-14 下午2.27.45.png](https://upload-images.jianshu.io/upload_images/11846892-409a43cbab7e2458.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 依然先转成list数组，用一个指针 cursor 从第二项开始扫描。
* 起初，用list[0]构建根节点，并让根节点入列。
* 节点出列，此时 cursor 指向它的左子节点值，cursor+1 指向它的右子节点值。
    1、如果子节点值是数值，则创建节点，并认出列的父亲，同时自己也是父亲，入列。
    2、如果子节点值为 'X'，什么都不用做，因为出列的父亲的 left 和 right 本来就是 null
* 可见，所有的真实节点都会在队列里走一遍，出列就带出儿子入列
![截屏2022-06-14 下午2.32.24.png](https://upload-images.jianshu.io/upload_images/11846892-e3efbecab4a0df7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实现代码如下：
```
const deserialize = (data) => {
  if (data == 'X') return null;

  const list = data.split(',');  // 序列化字符串split成数组

  const root = new TreeNode(list[0]); // 获取首项，构建根节点
  const queue = [root];          // 根节点推入队列
  let cursor = 1;                // 初始指向list第二项

  while (cursor < list.length) { // 指针越界，即扫完了序列化字符串
    const node = queue.shift();  // 考察出列的节点

    const leftVal = list[cursor];      // 它的左儿子的值
    const rightVal = list[cursor + 1]; // 它的右儿子的值

    if (leftVal != 'X') {              // 是真实节点
      const leftNode = new TreeNode(leftVal); // 创建左儿子节点
      node.left = leftNode;                   // 认父亲
      queue.push(leftNode);                   // 自己也是父亲，入列
    }
    if (rightVal != 'X') {
      const rightNode = new TreeNode(rightVal);
      node.right = rightNode;
      queue.push(rightNode);
    }
    cursor += 2; // 一次考察一对儿子，指针加2
  }
  return root;  // BFS结束，构建结束，返回根节点
};


```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：
https://leetcode.cn/problems/serialize-and-deserialize-binary-tree/solution/shou-hui-tu-jie-gei-chu-dfshe-bfsliang-chong-jie-f/











