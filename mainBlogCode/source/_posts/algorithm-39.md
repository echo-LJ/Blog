---
title: algorithm-39： 组合总和： (回溯+剪枝)-中等
date: 2023-08-09 15:34:08
tags: 算法
---
<meta name="referrer" content="no-referrer"/>


## 题目描述：给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有 不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。candidates 中的 同一个 数字可以 无限制重复被选取 。如果至少一个数字的被选数量不同，则两种组合是不同的。 对于给定的输入，保证和为 target 的不同组合数少于 150 个。

**示例1:**


```
输入：candidates = [2,3,6,7], target = 7
输出：[[2,2,3],[7]]
解释：
2 和 3 可以形成一组候选，2 + 2 + 3 = 7 。注意 2 可以使用多次。
7 也是一个候选， 7 = 7 。
仅有这两种组合。
```

**示例2:**
```

输入: candidates = [2,3,5], target = 8
输出: [[2,2,2,2],[2,3,3],[3,5]]
```
**示例3:**
```
输入: candidates = [2], target = 1
输出: []

```


## 题解方法一：回溯+剪枝

**`算法思路`**
[Krahets](https://leetcode.cn/problems/combination-sum/solutions/2363929/39-zu-he-zong-he-hui-su-qing-xi-tu-jie-b-9zx7/)

**`代码实现如下`**

```
function combinationSum(candidates, target) {
  function backtrack(state, target, choices, start, res) {
    // 子集和等于 target 时，记录解
    if (target === 0) {
      res.push([...state]);
      return;
    }
    // 遍历所有选择
    // 剪枝二：从 start 开始遍历，避免生成重复子集
    for (let i = start; i < choices.length; i++) {
     // 剪枝一：若子集和超过 target ，则直接结束循环
     // 这是因为数组已排序，后边元素更大，子集和一定超过 target
      if (target - choices[i] < 0) {
        break;
      }
      // 尝试：做出选择，更新 target, start
      state.push(choices[i]);
      //  # 进行下一轮选择
      backtrack(state, target - choices[i], choices, i, res);
      state.pop();
    }
  }
  const state = []; // 状态（子集）
  candidates.sort((a, b) => a - b); // 对 candidates 进行排序
  const start = 0; // 遍历起始点
  const res = []; // 结果列表（子集列表）
  backtrack(state, target, candidates, start, res);
  return res;
}


```

![截屏2023-08-09 下午4.20.30.png](https://upload-images.jianshu.io/upload_images/11846892-4d981753f4ff3201.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接:

* [ algorithm-39： 组合总和： (回溯+剪枝)-中等](https://leetcode.cn/problems/combination-sum/description/)


