---
title: 4 个你从未听说过的更强大 JavaScript 运算符
date: 2021-11-22 13:35:22
tags: Js
---
<meta name="referrer" content="no-referrer"/>

![1_Whvjdise1A-cNdXqTkiUVw.png](https://upload-images.jianshu.io/upload_images/11846892-93347174e21f02b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


JavaScript 很难掌握。写这篇文章是因为 JavaScript 是一种庞大的语言，其功能即使是经验丰富的开发人员也可能无法完全理解。[在上一篇文章中](https://javascript.plainenglish.io/4-powerful-javascript-operators-youve-never-heard-of-487df37114ad)，我重点介绍了您可能从未听说过的四个运算符。让我们通过学习另外四个未充分利用的运算符来继续加强我们的 JavaScript 技能

## 1. **`delete`**
`delete` 运算符的作用是从对象中删除属性。如果删除成功则返回真，否则返回假。虽然看起来很简单，但是`delete`有许多边缘情况使其行为变得棘手。
例如，如果您尝试删除一个不存在的属性，则不会删除任何属性并且返回 true。

```
const myTrip = {
    location: "Boston",
    budget: 600,
    duration: function(arg) { return arg }
}

function deleteProperty(object, property) {
    return delete object[property]
}

console.log(deleteProperty(myTrip, 'budget')) // deleted budget property returns -> true 
console.log(deleteProperty(myTrip, 'trainTickets')) // failed to locate and delete train-tickets -> true 

console.log(myTrip) // { location: 'Boston', duration: 6 }
```
通过let、var、const声明的基本类型的变量不能被`delete`删除，但是可以删除未用let、var、const声明的变量

```
const hello = 'hello'
goodbye = 'goodbye'

console.log(delete hello) // false
console.log(delete goodbye) // true


console.log(hello, goodbye=undefined) // hello undefined
```
创建变量而不正确声明它们通常是一个非常糟糕的想法。展望未来，我们假设所有原始类型都已正确声明并且不可删除。与函数不同，方法可以从对象中删除。
```
const myTrip = {
    location: "Boston",
    budget: 600,
    duration: function(arg) { return arg }
}

function deleteProperty(object, property) {
    return delete object[property]
}
console.log(myTrip) // { location: 'Boston', budget: 600, duration: [Function: duration] }

console.log(deleteProperty(myTrip, 'duration')) // deleted duration method returns -> true 

console.log(myTrip) // { location: 'Boston', budget: 600 }
```

内置对象上存在的属性，如`Array`和`Error`不能删除。它们被认为是不可配置的。非内置对象也可以使用方法`Object.defineProperty()`设置成不可配置的属性.
```
let myTrip = {
    location: "Boston",
    budget: 600,
    passports: true
}

Object.defineProperty(myTrip, 'passports', {configurable: false} )


function deleteProperty(object, property) {
    return delete object[property]
}

console.log(deleteProperty(myTrip, 'passports')) // can not delete non-config property 
                                                   // returns -> false

console.log(myTrip) // { location: 'Boston', budget: 600, passports: true }
```

最后，虽然`delete`可以与数组一起使用，但通常该方法`Array.splice()` 更可取。

## 2. **`instanceof`**

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

原文链接：[Four More Powerful JavaScript Operators You’ve Never Heard Of](https://javascript.plainenglish.io/4-powerful-javascript-operators-youve-never-heard-of-487df37114ad)

