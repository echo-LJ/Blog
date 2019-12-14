---
title: test
tags:
---


## 一个密码生成器，并同时写一个密码强度校验的方法 

```
/**
/*方法说明
 *@createPassword 密码范围 {0-9，A-Z, a-z}
 *@param
 * num {num} 生成密码长度
 *  b {num} 密码生成类型，1: 数字， 2:数字+小写，3.数字+大小写
 *@return {str} 
*/
**/
let createPassword = function (num, b) {
    let n = 0 //循环次数
    let arr = new Array // 随机数保存
    let Capitalization = () => Math.floor(Math.random() * b)// 随机取数0-2/0-1
    let randomNumber = [() => Math.floor(Math.random() * (57 - 48 + 1) + 48), () => Math.floor(Math.random() * (122 - 97 + 1) + 97), () => Math.floor(Math.random() * (90 - 65 + 1) + 65)] // 去随机值
    for (let i = 0; i < num; i++) {
        arr.push(randomNumber[Capitalization()]())
    }
    return arr.map(e => String.fromCharCode(e)).join('')
}

/*方法说明
 *@method passwordStrength 密码强度校验
 *@for 所属类名
 *@param 
 *        num{str} 密码
 *        len {num} 密码长度
 *@return {str} 返回密码等级

      str    ASCII
      0-9    48-57
      A-Z    65-90
      a-z    97-122

 */
let passwordStrength = function (num, len) {
    if(num.length<len) return '密码长度不够'
    let passwordLevel = ['弱', '普通', '较强', '强']
    let arr = new Array(4) // 记录密码等级
    num.split('').map(e => e.charCodeAt()).forEach(e => {
        if (e >= 48 && e <= 57) { arr[0] = 1 }
        else if (e >= 65 && e <= 90) { arr[1] = 1 }
        else if (e >= 91 && e <= 122) { arr[2] = 1 }
        else { arr[3] = 1 }
    })
    return passwordLevel[arr.reduce((a, b) => a + b) - 1]
}

```