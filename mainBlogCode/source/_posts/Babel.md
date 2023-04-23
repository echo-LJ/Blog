---
title: Babel源码详解
date: 2023-04-23 13:41:30
tags: Babel
---
<meta name="referrer" content="no-referrer"/>


[Babel](https://Babeljs.io/docs/Babel-traverse)是一个广泛使用的 JavaScript 编译器，本质上是对`AST`的操控，从`js`到`js`的转化。

![image.png](https://res.cloudinary.com/dvtfhjxi4/image/upload/v1624407593/origin-of-ray/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20210623081837_ikbdn8.png)

**`编译器的三个阶段：`** `解析【Parser】 => 转换【Transformer】 => 生成【Generator】`


几个主要的步骤：

1、解析生成 AST 树；
2、遍历 AST 树，通过匹配转换规则完成转换生成新的AST 树；
3、将转换后的语法树节数再次遍历，把 AST 节点重新拼接成字符串形式；
4、对 Babel 生成的转换结果进行美化、压缩等操作，生成最终的目标 JavaScript 代码。

### 解析【Parser】- @Babel/parser

`Parser模块`是 Babel 编译器的入口模块之一， 负责将JavaScript转换解析成`AST`。
 
 Parser支持多个 JS 语言规范，包括 ES6 / ES7 / JSX / TS / Flow 等

**1、词法分析阶段`Tokenizer`:** Tokenizer把代码字符串拆分成Token数组

字符串形式的代码转换为令牌（tokens）流,令牌类似于AST中的节点。


#####  Babel调用`acorn` (快速的 JavaScript 解析器)中的`tokenizer`函数处理js中的es6源代码，对源代码进行分词操作,生成Tokens数组。
* `acorn中tokenizer`函数生成的每个token具有2个属性:
①`type`: 当前 Token 的语法类型, 包含 `StringLiteral【string】`、`NumericLiteral【number】`、`BooleanLiteral【boolean】`、`Punctuator`、`Identifier【undefined】`、`Keyword`等
②`value`:当前 Token 的字面值

```
对于 const data = "test"; 经过分词就是下边的结果
[
{ type: 'Keyword', value: 'const' },
{ type: 'Identifier', value: 'data' },
{ type: 'Punctuator', value: '=' },
{ type: 'String', value: '"test"' }
]
```

* Babel中`tokenizer`方法基于`acorn 的 tokenizer() 函数`进行封装的生成Token数组（不包含位置信息），遍历 Token 数组，将每个 Token 的`位置信息`添加到其属性中。
③`loc`: Token 在源代码中的位置信息。该属性是一个包含 start 和 end 两个属性的对象，这两个属性都是包含 line 和 column 两个属性的对象。表示 Token 开始和结束的行号和列号。


**2、语法分析阶段`parser()`:** parser把Token数组转换为AST树

把一个令牌流转化为AST的形式，同时这个阶段会把令牌中的信息转化为AST的表述结构.



##### Babel 将 acorn 返回的 tokens 序列传递给 Babel 内部的 Parser，Parser 会根据预定义的词法规则，类型和值等信息，将 Token 序列组合成一个个 AST 节点。

Babel 会将 Token 序列转化成一个类似 AST 的数据结构。这个数据结构被称为抽象语法树。

Babel 的 Parser 模块并不是直接对 Token 序列进行解析生成 AST 树，而是通过`递归下降分析法`将 Token 序列转换为抽象语法树（AST）的节点对象，然后进行语法分析、语义分析、优化等一系列操作，最终生成符合目标代码规范的 AST 树.



```
import { Parser } from "@Babel/parser";

function parse(tokens, options) {
  // 构建一个选项对象，包括了从 options 和 Babel 默认选项中获取的所有选项。
  options = Object.assign(
    {
      plugins: [],
      allowAwaitOutsideFunction: true,
      allowImportExportEverywhere: true,
      allowReturnOutsideFunction: true,
      allowSuperOutsideMethod: true,
      sourceType: "module",
      strictMode: null,
      ranges: false,
      tokens: false,
      errorRecovery: false,
      createParenthesizedExpressions: false,
      ranges: false,
    },
    options || {},
    Parser.parseDefaults
  );

  // 创建一个 Parser 实例对象，用于对 Token 序列进行语法分析生成 AST 树。
  const parser = new Parser(options, undefined);

  // 通过调用 Parser 实例对象的 `parse()` 方法，将 Token 序列解析成 AST 树。
  const ast = parser.parse(tokens);

  // 返回 AST 树
  return ast;
}

export default parse;
```
`递归下降分析法:`递归下降分析法是确定的自上而下分析法，这种分析法要求文法是LL(1)文法。 为每个非终结符编制一个递归下降分析函数，每个函数名是相应的非终结符，函数体则是根据规则右部符号串的结构和顺序编写。 子程序相互递归调用

* 优点：递归下降分析法简单、直观，易于构造分析程序。

* 缺点：对文法要求高，必须是LL(1)文法，同时由于递归调用较多，影响分析器的效率。


### 转换【Transformer】- @Babel/traverse

Babel的Transformer模块负责将AST转换为另一个AST。Transformer组件包含了一组插件，可以将AST节点的类型进行变换（Transform）生成新的AST语法书。每个插件都有一个visitor对象，用于描述需要转换的节点以及转换处理函数。

例如，当遇到某个节点的时候，Transformer会查询所有的Plugins中注册的Visitor，选择与之匹配的Transformer进行转换处理，最终输出一个新的 AST。这个过程构成了AST的遍历过程。


`@Babel/traverse`示例源码:
```
const babylon = require("@Babel/parser");
const traverse = require("@Babel/traverse").default;
let ast = babylon.parse(code, { sourceType: 'module', plugins: ['jsx'] });
const visitor = {
  FunctionDeclaration: function(path) {
    // 操作节点
  },
  Identifier: function(path) {
    // 操作节点
  }
}

traverse(ast, visitor)

console.dir(ast, {depth: null})
```
`traverse 接收俩个参数:`

* AST: @Babel/parser生成的AST语法树.
* visitor: 是一个对象，包含了多个 visitor 方法，用于定义需要遍历的节点类型以及对应转换处理函数。

visitor包含了进入节点的方法（enter）、 操作节点的方法、退出节点的方法（exit），在遍历 AST 时，如果遇到了对应ast的类型节点，便会执行 visitor 对应的方法.

`visitor方法中的path参数:`

path 是 NodePath 类型的实例，它是一个用于管理 AST 节点的路径对象。NodePath 对象是对 AST 节点的一种包装，它包含了节点自身的信息，以及由根节点到当前节点的路径信息，可以方便地对 AST 树进行分析、修改和重构。实现ast节点之间的相互关联。



**基本的 NodePath 包含以下属性和方法**
* `node`：表示当前节点本身的信息，通常是一个 JavaScript 对象。
* `parent`：表示当前节点的父节点。
* `parentPath`：表示当前节点的父节点路径。
* `key`：表示当前节点在父节点中的位置，通常是一个数字或字符串。
* `listKey`：表示当前节点在父节点的集合中的位置，通常是一个数字或字符串。
* `type`：表示当前节点的类型，通常是一个字符串。
* `scope`：表示当前节点所在的作用域。
* `context`：表示当前节点的上下文。
* `setData(data)`：设置当前路径的数据。
* `getData()`：获取当前路径的数据。
* `get(key: string, context?: string)`: NodePath：获取当前路径下名为 key 的子节点路径。
* `getSibling(key: string)`: NodePath：获取当前路径下同级的名为 key 的节点路径。
* `insertBefore(nodes: Node | Array<Node>)`: void：在当前节点之前插入一个或多个节点。
* `insertAfter(nodes: Node | Array<Node>)`: void：在当前节点之后插入一个或多个节点。
* `replaceWith(node: Node)`: void：用传入的节点替换掉当前节点。
* `remove()`: void：从父节点中删除当前节点。

**常用的核心插件包括:**

* `@Babel/plugin-transform-arrow-functions`：将 ES6 箭头函数转换成普通函数表达式。
* `@Babel/plugin-transform-block-scoping`：将 ES6 let/const 块级作用域语法转换成 var 语句的混合形式。
* `@Babel/plugin-transform-classes`：将 ES6 类语法转换成 ES5 构造函数和原型方法的形式。
```
// es6
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return `(${this.x}, ${this.y})`;
  }
}
//es5

function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function() {
  return "(" + this.x + ", " + this.y + ")";
};

```
* `@Babel/plugin-transform-destructuring`：将 ES6 解构语法转换成普通的赋值语句。
* `@Babel/plugin-transform-for-of`：将 ES6 for...of 循环语法转换成普通的 for 循环语法。
* `@Babel/plugin-transform-modules-commonjs`：将 ES6 模块语法转换成 CommonJS 模块的形式。

* `@Babel/plugin-transform-object-assign`：将 Object.assign() 方法转换成其他实现方式。
* `@Babel/plugin-transform-parameters`：将 ES6 函数参数语法转换成普通的函数参数的形式。
* `@Babel/plugin-transform-spread`：将 ES6 扩展运算符语法转换成普通的函数参数的形式。
```
// es6
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const mergedArr = [...arr1, ...arr2];
console.log(mergedArr); // [1, 2, 3, 4, 5, 6]
// es5
var arr1 = [1, 2, 3];
var arr2 = [4, 5, 6];
var mergedArr = [].concat(arr1, arr2);
console.log(mergedArr); // [1, 2, 3, 4, 5, 6]
```
* `@Babel/plugin-transform-template-literals`：将 ES6 模板字符串语法转换成字符串拼接的形式。


### 生成【Generator】-@babel/generator
Generator通过将 AST 转化为源代码，并生成 source-map对象。


`@babel/generator` 是 Babel 转换过程的最后一步，它会接收处理后的 AST 树，遍历根据 AST 节点类型和 Visitor 列表定义的代码生成器函数，生成 JavaScript 代码字符串，并在生成的代码字符串中添加分号或者空格等细节信息，并使用 source-map 模块添加源代码映射信息


**@babel/generator** 模块源码的基本逻辑:x

* 初始化 Visitor 列表: 

@babel/generator 会初始化一个 Visitor 列表，包含了所有可能出现的 AST 节点类型以及对应的生成器函数，每个生成器函数会接收两个参数：节点对象和 State 对象，使用这两个参数`生成对应的 JavaScript 代码字符串`。

* 定义 State 对象:
State 对象是一个状态对象，用于在`遍历 AST 树时存储一些上下文信息，帮助生成器函数生成 JavaScript 代码字符串`。它包含了 write 方法、newline 方法、indent 方法等方法，用于控制输出的代码格式、缩进和换行等。

* 遍历 AST 树
当 @babel/generator 被调用时，它会接收 AST 树对象作为参数，开始遍历 AST 树，并对每个节点调用生成器函数，生成对应的 JavaScript 代码字符串。在输出代码字符串时，@babel/generator 依据 AST 节点类型和 Visitor 列表中预定义的处理函数，将节点转换为对应的代码字符串，并将其添加到 State 对象中

* 处理自动分号插入问题
生成过程中，@babel/generator 还会判断分号的插入问题。默认情况下，JavaScript 引擎会自动检测代码行尾的分号是否存在，如果不存在就会自动插入。不过这个过程在某些场景下会出现不可预测的行为。为了解决这种问题，@babel/generator 会判断节点是否要插入分号，并在生成 JavaScript 代码字符串时添加对应的分号。

* 处理源代码映射，以支持检查和调试。
@babel/generator 采用源代码映射的方式将 JavaScript 代码字符串对应到源代码的位置。这个过程主要依赖于`source-map` 模块。在生成 JavaScript 代码字符串和对应的 source map 后，@babel/generator 返回一个对象，包含了代码字符串和对应的 source map 对象。



参考链接:
* [深入Babel原理系列（三）Tokenizer](https://sunra.top/2021/06/22/Babel-1/)
* [在线AST转换器](https://sunra.top/2021/06/22/Babel-1/)
* [babel 的编译过程](https://juejin.cn/post/7142158641628446733#heading-4)