---
title: algorithm-39： 组合总和： (回溯+剪枝)-中等
date: 2023-08-09 17:11:05
tags: 算法
---
<meta name="referrer" content="no-referrer"/>


## 题目描述：给定一个候选人编号的集合 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

注意：解集不能包含重复的组合。。

**示例1:**


```
输入: candidates = [10,1,2,7,6,1,5], target = 8,
输出:
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

**示例2:**
```

输入: candidates = [2,5,2,1,2], target = 5,
输出:
[
[1,2,2],
[5]
]
```



## 题解方法一：回溯+剪枝

**`算法思路`**
参考第39题

**`代码实现如下`**

```
function combinationSum2(candidates, target) {
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
      if (i > start && choices[i] == choices[i - 1]){
        continue
      }
                    
      // 尝试：做出选择，更新 target, start
      state.push(choices[i]);
      //  # 进行下一轮选择
      backtrack(state, target - choices[i], choices, i+1, res);
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


