---
title: algorithm-54： 螺旋矩阵 （解法）-中等
date: 2023-08-15 14:14:46
tags: 算法
---
<meta name="referrer" content="no-referrer"/>


## 题目描述：给你一个 m 行 n 列的矩阵 matrix ，请按照 顺时针螺旋顺序 ，返回矩阵中的所有元素。


**示例1:**

![截屏2023-08-15 下午2.16.17.png](https://upload-images.jianshu.io/upload_images/11846892-7d51177b3bca5e8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

**示例2:**

![截屏2023-08-15 下午2.16.37.png](https://upload-images.jianshu.io/upload_images/11846892-ffba3732984c36f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
输入：matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
输出：[1,2,3,4,8,12,11,10,9,5,6,7]
```



## 题解方法一：双指针

**`解题思路:`**

* 空值处理： 当 matrix 为空时，直接返回空列表 [] 即可。
* 初始化： 矩阵 左、右、上、下 四个边界 l , r , t , b ，用于打印的结果列表 res 。
* 循环打印： “从左向右、从上向下、从右向左、从下向上” 四个方向循环打印。
    1、根据边界打印，即将元素按顺序添加至列表 res 尾部。
    2、边界向内收缩 1 （代表已被打印）。
    3、判断边界是否相遇（是否打印完毕），若打印完毕则跳出。
* 返回值： 返回 res 即可。

**`代码实现如下`**
```
function spiralOrder(matrix) {
  if (matrix.length === 0) {
    return [];
  }
  let l = 0,
    r = matrix[0].length - 1,
    t = 0,
    b = matrix.length - 1,
    x = 0;
  let res = new Array((r + 1) * (b + 1));
  while (true) {
    for (let i = l; i <= r; i++) res[x++] = matrix[t][i]; // left to right
    if (++t > b) break;
    for (let i = t; i <= b; i++) res[x++] = matrix[i][r]; // top to bottom
    if (l > --r) break;
    for (let i = r; i >= l; i--) res[x++] = matrix[b][i]; // right to left
    if (t > --b) break;
    for (let i = b; i >= t; i--) res[x++] = matrix[i][l]; // bottom to top
    if (++l > r) break;
  }
  return res;
}
```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [algorithm-54： 螺旋矩阵 （解法）-中等](https://leetcode.cn/problems/spiral-matrix/submissions/)


