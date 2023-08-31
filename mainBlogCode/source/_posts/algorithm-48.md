---
title: algorithm-48:旋转图像-中等
date: 2023-08-31 10:53:47
tags: 算法
---

<meta name="referrer" content="no-referrer"/>


## 题目描述：给定一个 n × n 的二维矩阵 matrix 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在 原地 旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要 使用另一个矩阵来旋转图像


**示例1:**


![截屏2023-08-31 上午10.55.38.png](https://upload-images.jianshu.io/upload_images/11846892-497d7d000f809ed9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

**示例2:**

![截屏2023-08-31 上午10.55.57.png](https://upload-images.jianshu.io/upload_images/11846892-762b8baacc4aeeeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



```
输入：matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
输出：[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```



## 题解方法一：辅助矩阵

**`解题思路:`**
根据题意： 矩阵顺时针旋转 90º 后，可找到以下规律：

* 「第 i 行」元素旋转到「第 n−1−i 列」元素；
* 「第 j 列」元素旋转到「第 j 行」元素；
* 对于矩阵任意第 i 行、第 j 列元素 matrix[i][j] ，矩阵旋转 90º 后「元素位置旋转公式」为：

```
matrix[i][j]  →  matrix[j][n−1−i]
原索引位置 →  旋转后索引位置
```


**`代码实现如下`**
```
function rotate(matrix) {
  const n = matrix.length;
  // 创建一个临时矩阵进行深拷贝
  const tmp = matrix.map((row) => [...row]);
  
  // 根据元素旋转公式，遍历修改原矩阵 matrix 的各元素
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      matrix[j][n - 1 - i] = tmp[i][j];
    }
  }
}

```
 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [algorithm-48:旋转图像-中等](https://leetcode.cn/problems/rotate-image/solutions/)


扩展知识：
* 数组深拷贝方法：
```
const tmp = matrix.map((row) => [...row]);
```


