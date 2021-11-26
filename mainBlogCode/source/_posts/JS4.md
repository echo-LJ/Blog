---
title: 4 个你从未听说过的强大 JavaScript 运算符
date: 2021-06-16 13:35:22
tags: Js
---
<meta name="referrer" content="no-referrer"/>

![1_U16Sxl3a_xpV8R3xDGgMdQ (1).jpeg](https://upload-images.jianshu.io/upload_images/11846892-6597b3cbb438f927.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


你有没有花一个下午阅读[Mozilla 文档](https://developer.mozilla.org/zh-CN/docs/Web)？如果您有的话，您就会知道网上有很多关于 JavaScript 的信息。这使得很容易忽略更不寻常的 JavaScript 运算符。
然而，仅仅因为这些运算符不常见并不意味着它们不强大！它们在语法上看起来很相似，但一定要阅读它们，因为它们以不同的方式工作。

让我们开始吧！

## 1. **`??运算符`**
在 JavaScript 中，该`??`运算符被称为`nullish`运算符(null/undefined)。如果它不是null/undefined，则此运算符将返回第一个参数，否则，它将返回第二个参数。
![下载 (3).png](https://upload-images.jianshu.io/upload_images/11846892-db18391e1cc3c50d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
null ?? 5 // => 5
3 ?? 5 // => 3
```
在为变量分配默认值时，JavaScript 开发人员传统上依赖于逻辑`OR`运算符，如下所示。
```
var prevMoney = 1
var currMoney = 0
var noAccount = null
var futureMoney = -1

function moneyAmount(money) {
  return money || `You currently do not own an account in the bank`
}

console.log(moneyAmount(prevMoney)) // => 1
console.log(moneyAmount(currMoney)) // => `You currently do not own an account in the bank`
console.log(moneyAmount(noAccount)) // => `You currently do not own an account in the bank`
console.log(moneyAmount(futureMoney))//  => -1
```
上面我们创建了一个函数`moneyAmount`，负责返回用户的当前余额。我们使用`||`来识别没有帐户的用户。当 money 为 0 或者 null 的时候都会返回在当前银行没有这个账户，但是实际上账户是可能为 0 的 。在上面的示例中，`||`运算符将 0 视为虚假值，因此不会识别出我们的用户拥有 0 美元的帐户。下面，让我们通过使用`??`运算符来解决这个问题。

```
var currMoney = 0
var noAccount = null

function moneyAmount(money) {
  return money ?? `You currently do not own an account in the bank`
}
 moneyAmount(currMoney) // => 0
 moneyAmount(noAccount) // => `You currently do not own an account in the bank`
```
总结一下， `??` 运算符允许我们分配默认值，同时识别出0 并忽略null等虚假值。

## 2. **`??=运算符`**
`??=`又称为逻辑 null/undefined 赋值操作符，与我们之前学到的内容密切相关。让我们看看它们是如何联系在一起的。
```
var x = null
var y = 5

console.log(x ??= y) // => 5
console.log(x = (x ?? y)) // => 5
```
如果当前值为null/undefined，则此`??=运算符`才会分配一个新值。上面的例子强调了这个运算符本质上是null/undefined赋值的语法糖。接下来，让我们看看这个运算符与默认参数有何不同。

```
function gameSettingsWithNullish(options) {
  options.gameSpeed ??= 1
  options.gameDiff ??= 'easy'
  return options
}


function gameSettingsWithDefaultParams(gameSpeed=1, gameDiff='easy') {
  return {gameSpeed, gameDiff}
}

gameSettingsWithNullish({gameSpeed: 2, gameDiff: 'busy'}) // => {gameSpeed: 2, gameDiff: "busy"}
gameSettingsWithNullish({gameSpeed:'', gameDiff: undefined})// => {gameSpeed: "", gameDiff: "easy"}
gameSettingsWithNullish({gameSpeed: null, gameDiff: null}) // => { gameSpeed: 1, gameDiff: 'easy' }
gameSettingsWithDefaultParams(null, null) // => { gameSpeed: null, gameDiff: null }
gameSettingsWithDefaultParams('', undefined) // => {gameSpeed: "", gameDiff: "easy"}
```
上述函数处理空值的方式有一个值得注意的区别。 默认参数将会被null覆盖掉，`??=运算符`不会。默认参数和null/undefined赋值都不会覆盖 未定义 的值。
[阅读更多](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_nullish_assignment)

## 3. **`?. 操作符`**
可选的链接操作符` ?. `允许开发人员读取深度嵌套在一个对象链中的属性值，而不必沿途显式验证每个引用。当引用为 null 时，表达式停止计算并返回 undefined，让我们来看一个例子。
```
var travelPlans  = {
  destination: 'DC',
  monday: {
    location: 'National Mall',
    budget: 200
  }
};

const tuesdayPlans = travelPlans.tuesday?.location;
console.log(tuesdayPlans) // => undefined
```
现在，让我们把迄今为止所学到的一切结合起来，把星期二添加到我们的新旅行计划中去吧
```
function addPlansWhenUndefined(plans, location, budget) {
  if (plans.tuesday?.location === undefined) {
    var newPlans = {
      plans,
      tuesday: { location: location ?? "Park", budget: budget ?? 200 },
    };
  } else {
    newPlans ??= plans; //will only override if newPlans is undefined
    console.log("Plans have already been added!");
  }
  return newPlans;
}

var newPlans = addPlansWhenUndefined(travelPlans, "Ford Theatre", null);
console.log(newPlans) // => { plans:
                  //{ destination: 'DC',
                  // monday: { location: 'National Mall', budget: 200 } },
                  // tuesday: { location: 'Ford Theatre', budget: 200 } }

newPlans = addPlansWhenUndefined(newPlans, null, null) // logs => Plans have already been added!
                                                      // returns => newPlans object
```
我们现在已经创建了一个函数，该函数将计划添加到当前没有嵌套属性 的对象中`tuesday.location`。我们还使用 ` ?? 运算符`来提供默认值。此函数将错误地接受像“0”这样的值作为有效参数。这意味着我们的预算可以设置为零而不会出现任何错误。
## 4. **`?  操作符`**
三元运算符?接受三个操作数：一个条件，一个条件为真时执行的表达式，以及一个条件为假时执行的表达式。让我们看看它的实际效果。
```
function checkCharge(charge) {
return (charge > 0) ? 'Ready for use' : 'Needs to charge'
}

console.log(checkCharge(20)) // => 'Ready for use'
console.log(checkCharge(0)) // => 'Needs to charge'
```
如果您对 JavaScript 有一定的了解，那么您之前可能已经见过三元运算符。但是，您知道三元运算符可以用于变量赋值吗？
```
var budget = 0
var transportion = (budget > 0) ? 'Train' : 'Walking'
console.log(transportion) // => 'Walking'
```
我们甚至可以用它来复制`null/undefined赋值`的行为。
```
var x = 6
var x = (x !== null || x !== undefined) ? x : 3
console.log(x) // => 6
```
现在让我们通过创建一个函数来泛化这种行为！
```
function nullishAssignment(x,y) {
  return (x == null || x == undefined) ? y : x
}

var x = nullishAssignment(null, 8) // => 8
var y = nullishAssignment(4,8) // => 4
```
在结束之前，让我们使用三元运算符来重构前面示例中的函数。
```
function addPlansWhenUndefined(plans, location, budget) {
  var newPlans =
    plans.tuesday?.location === undefined
      ? {
          ...plans,
          tuesday: { location: location ?? "Park", budget: budget ?? 200 },
        }
      : console.log("Plans have already been added!");
  newPlans ??= plans;
  return newPlans;
}
```
## 结束语
---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

原文链接：[4 Powerful JavaScript Operators You’ve Never Heard Of](https://javascript.plainenglish.io/4-powerful-javascript-operators-youve-never-heard-of-487df37114ad)

