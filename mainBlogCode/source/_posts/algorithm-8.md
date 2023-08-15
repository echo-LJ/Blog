---
title: leeCode-8： 字符串转换整数 (atoi)-中等
date: 2023-08-01 16:23:09
tags: 算法
---


<meta name="referrer" content="no-referrer"/>


## 题目描述：  请你来实现一个 myAtoi(string s) 函数，使其能将字符串转换成一个 32 位有符号整数（类似 C/C++ 中的 atoi 函数）。

函数 myAtoi(string s) 的算法如下：
1、读入字符串并丢弃无用的前导空格
2、检查下一个字符（假设还未到字符末尾）为正还是负号，读取该字符（如果有）。 确定最终结果是负数还是正数。 如果两者都不存在，则3、假定结果为正。
4、读入下一个字符，直到到达下一个非数字字符或到达输入的结尾。字符串的其余部分将被忽略。
5、将前面步骤读入的这些数字转换为整数（即，"123" -> 123， "0032" -> 32）。如果没有读入数字，则整数为 0 。必要时更改符号（从步骤 2 开始）。
6、如果整数数超过 32 位有符号整数范围  [-2^{31}, 2^{31} - 1]，需要截断这个整数，使其保持在这个范围内。具体来说，小于 -2^{31} 的整数应该被固定为 -2^{31} ，2^{31} - 1 的整数应该被固定为 2^{31} - 1 。
返回整数作为最终结果。
**示例1:**


```
输入：s = "4193 with words"
输出：4193
解释：
第 1 步："4193 with words"（当前没有读入字符，因为没有前导空格）
         ^
第 2 步："4193 with words"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
         ^
第 3 步："4193 with words"（读入 "4193"；由于下一个字符不是一个数字，所以读入停止）
             ^
解析得到整数 4193 。
由于 "4193" 在范围 [-231, 231 - 1] 内，最终结果为 4193 。
```

**示例2:**

```
输入：s = "42"
输出：42
解释：加粗的字符串为已经读入的字符，插入符号是当前读取的字符。
第 1 步："42"（当前没有读入字符，因为没有前导空格）
         ^
第 2 步："42"（当前没有读入字符，因为这里不存在 '-' 或者 '+'）
         ^
第 3 步："42"（读入 "42"）
           ^
解析得到整数 42 。
由于 "42" 在范围[-2^{31}, 2^{31} - 1] 内，最终结果为 42 。
```

**示例3:**

```
输入：s = "   -42"
输出：-42
解释：
第 1 步："   -42"（读入前导空格，但忽视掉）
            ^
第 2 步："   -42"（读入 '-' 字符，所以结果应该是负数）
             ^
第 3 步："   -42"（读入 "42"）
               ^
解析得到整数 -42 。
由于 "-42" 在范围 [-231, 231 - 1] 内，最终结果为 -42 。
```


## 题解方法一：

**`解题思路`**
* 首部空格： 删除即可。
* 符号位： 三种情况，即 '+' , '−' , ''无符号" ；新建一个变量保存符号位，返回前判断正负即可。
* 非数字字符： 遇到首个非数字的字符时，应立即返回。
* 数字字符：
    1、字符转数字： “此数字的 ASCII 码” 与 “ 000 的 ASCII 码” 相减即可。Js用Number转换即可
    2、数字拼接： 若从左向右遍历数字，设当前位字符为 c ，数字结果为 res，则数字拼接公式为：`res=10×res+Number(c)`
    
**代码实现如下：**
```
var myAtoi = function(s) {
    s = s.trim(); // 删除首尾空格
    if (!s) return 0; // 字符串为空则直接返回
    let res = 0, i = 1, sign = 1;
    const int_max = Math.pow(2, 31) - 1 //2147483647
    const  int_min = -Math.pow(2, 31)  // -2147483647
    bndry = Math.floor(Math.pow(2, 31) / 10); // 214748364 //boundary边界值
    if (s[0] === '-') {
      // 保存负号
      sign = -1; 
    } else if (s[0] !== '+'){
      // 若无符号位，则需从 i = 0 开始数字拼接
       i = 0; 
    }
    
    for (let j = i; j < s.length; j++) {
      const c = s[j];
      if (!('0' <= c && c <= '9')) break; // 遇到非数字的字符则跳出
      // res >214748364 终止循环 已进入最大值
      // res === bndry && c > '7' 终止循环 已进入最大值， 否则res会比2147483647还要大
      if (res > bndry || (res === bndry && c > '7')) return sign === 1 ? int_max : int_min; // 数字越界处理
      res = 10 * res +  Number(c); // 数字拼接
    }
    return sign * res;
  }


```

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:

* [字符串转换整数 (atoi)-中等](https://leetcode.cn/problems/string-to-integer-atoi/solutions/2361399/8-zi-fu-chuan-zhuan-huan-zheng-shu-atoiq-a2e8/)