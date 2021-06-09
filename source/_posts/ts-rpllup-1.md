---
title: TypeScript+Rollup快速构建完整配置并发布npm包
date: 2021-06-02 10:04:47
tags: npm
---

<meta name="referrer" content="no-referrer"/>
#### 介绍
**`typescript-library-starter`**
它是一个开源的 TypeScript 开发基础库的脚手架工具，可以帮助我们快速初始化一个 TypeScript 项目，这是它的[官网地址）](https://github.com/alexjoverm/typescript-library-starter)，有兴趣的同学可以了解下。

## 初始化项目
---
```
git clone https://github.com/alexjoverm/typescript-library-starter.git typeScript-sdk-demo
cd typeScript-sdk-demo

npm install
```

## 文件目录介绍
---
安装依赖结束后，打开项目工程目录，**`TypeScript library starter`** 生成的目录结构如下：
```
├── CONTRIBUTING.md
├── LICENSE 
├── README.md
├── code-of-conduct.md
├── node_modules
├── package-lock.json
├── package.json
├── rollup.config.ts    // rollup 配置文件
├── src                 // 源码目录
├── test                // 测试目录
├── tools               // 发布到 GitHub pages 以及 发布到 npm 的一些配置脚本工具
├── tsconfig.json       // TypeScript 编译配置文件
└── tslint.json         // TypeScript lint 文件
```

## 工具集成
---
使用 **`TypeScript library starter`** 创建的项目集成了很多优秀的开源工具：
* 使用 `RollupJS` 帮助我们打包。
* 使用 `Prettier` 和 `TSLint` 帮助我们格式化代码以及保证代码风格一致性。
* 使用 `TypeDoc` 帮助我们自动生成文档并部署到 GitHub pages。
* 使用 `Jest` 帮助我们做单元测试。
* 使用 `Commitizen` 帮助我们生成规范化的提交注释。
* 使用 `Semantic release` 帮助我们管理版本和发布。
* 使用 `husky` 帮助我们更简单地使用 git hooks。
* 使用 `Conventional changelog` 帮助我们通过代码提交信息自动生成 change log。

## 关联远程git仓库
---
当前创建好的代码库与 github 远程仓库是还未关联的，也就不能提交到远程仓库，如何关联呢？
```
$ git remote add origin 仓库地址
```
仓库地址 为之前新建的仓库，查看如下：
![1581842457980-a25385f2-300b-4ccb-b89e-96c074264c33.png](https://upload-images.jianshu.io/upload_images/11846892-5c2d1b496fd344d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


接下来查看是否关联成功：
```
$ git remote -v
```

## 开发npm包
在我们的 src 目录下新建 index.ts 进行功能代码的编写，在此我们简单写一个 add 函数作为此次的发布的 npm 包所提供的功能。
```
// src/index.ts 
export default class TsSdk {
    /**
   * return sum
   * @static
   * @param {number} a 
   *  @param {number} a 
   */
  static add(a: number, b: number): number {
    return a+b
  }
}


```
但是在开发过程中往往需要开一个本地服务来测试我们的代码，也就是要写一个 demo，这时候就要用到 webpack 了。

## demo编写
---

我们会利用 Node.js 的 `express` 库去运行我们的 demo，利用 `webpack` 来作为 demo 的构建工具。
### 依赖安装
我们先来安装一些编写 demo 需要的依赖包，如下：
```
npm install -D webpack webpack-dev-middleware webpack-hot-middleware ts-loader  tslint-loader express
```

* `webpack` 是打包构建工具
* `webpack-dev-middleware` 和 `webpack-hot-middleware` 是 2 个 express 的 webpack 中间件
* `ts-loader`和 `tslint-loader` 是 webpack 需要的 TypeScript 相关 loader
* `express` 是 Node.js 的服务端框架。
### 编写 webpack 配置文件
根目录下创建 webpack 配置文件 `examples/webpack.config.js`：
```
const fs = require('fs')
const path = require('path')
const webpack = require('webpack')
module.exports = {
  mode: 'development',
  /**
   * 我们会在 examples 目录下建多个子目录
   * 我们会把不同章节的 demo 放到不同的子目录中
   * 每个子目录的下会创建一个 app.ts
   * app.ts 作为 webpack 构建的入口文件
   * entries 收集了多目录个入口文件，并且每个入口还引入了一个用于热更新的文件
   * entries 是一个对象，key 为目录名
   */
  entry: fs.readdirSync(__dirname).reduce((entries, dir) => {
    const fullDir = path.join(__dirname, dir)
    const entry = path.join(fullDir, 'app.ts')
    if (fs.statSync(fullDir).isDirectory() && fs.existsSync(entry)) {
      entries[dir] = ['webpack-hot-middleware/client', entry]
    }
    return entries
  }, {}),
  /**
   * 根据不同的目录名称，打包生成目标 js，名称和目录名一致
   */
  output: {
    path: path.join(__dirname, '__build__'),
    filename: '[name].js',
    publicPath: '/__build__/'
  },
  module: {
    rules: [
      {
        test: /\.ts$/,
        enforce: 'pre',
        use: [
          {
            loader: 'tslint-loader'
          }
        ]
      },
      {
        test: /\.tsx?$/,
        use: [
          {
            loader: 'ts-loader',
            options: {
              transpileOnly: true
            }
          }
        ]
      }
    ]
  },
  resolve: {
    extensions: ['.ts', '.tsx', '.js']
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NoEmitOnErrorsPlugin()
  ]
}
```
### 编写 server 文件
在 examples 目录下创建 `server.js` 文件：
```
const express = require('express')
const webpack = require('webpack')
const webpackDevMiddleware = require('webpack-dev-middleware')
const webpackHotMiddleware = require('webpack-hot-middleware')
const WebpackConfig = require('./webpack.config')

const app = express()
const compiler = webpack(WebpackConfig)

app.use(webpackDevMiddleware(compiler, {
  publicPath: '/__build__/',
  stats: {
    colors: true,
    chunks: false
  }
}))

app.use(webpackHotMiddleware(compiler))

app.use(express.static(__dirname))

const port = process.env.PORT || 9090
module.exports = app.listen(port, () => {
  console.log(`Server listening on http://localhost:${port}, Ctrl+C to stop`)
})
```
### 编写 demo 代码
首先在 examples 目录下创建 index.html 和 global.css，作为所有 demo 的入口文件已全局样式文件。
`index.html`
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>typescript-npm-demo examples</title>
    <link rel="stylesheet" href="/global.css">
  </head>
  <body style="padding: 0 20px">
    <h1>typescript-npm-demo examples</h1>
    <ul>
      <li><a href="basic">Basic</a></li>
    </ul>
  </body>
</html>
```
`global.css`
```
html, body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  color: #2c3e50;
}
ul {
  line-height: 1.5em;
  padding-left: 1.5em;
}
a {
  color: #7f8c8d;
  text-decoration: none;
}
a:hover {
  color: #4fc08d;
}
```
然后在 examples 目录下创建 basic 目录，作为本章节的 demo 目录，在该目录下再创建 index.html 和 app.ts 文件

`basic/index.html`
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Basic example</title>
  </head>
  <body>
    <p>add result is equal to: <i class="result"></i></p>
    <script src="/__build__/basic.js"></script>
  </body>
</html>
```
`basic/app.ts`
```
import TsSdk from '../../src/index.ts'

const addResult = TsSdk.add(1, 2)
document.querySelector('.result').innerText = addResult
```

### 运行 demo
接着我们在 package.json 中去新增一个 npm script：
```
"dev": "node examples/server.js"
```
```
$ npm run dev
```
相当于执行了 `node examples/server.js`，会开启我们的 server。
接着我们打开 chrome 浏览器，访问 http://localhost:9090/ 即可访问我们的 demo 了
![截屏2021-06-02 上午11.50.34.png](https://upload-images.jianshu.io/upload_images/11846892-4b79b28c4198dcda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 四、单元测试
---
### Jest 配置
在 `package.json`
 文件中有 jest 字段，对应 Jest 配置：

```
"jest": {
  
  // 将ts转换成js,因为实用ts编写测试代码
  "transform": {
    ".(ts|tsx)": "ts-jest"
  },

  // 测试环境，这里原来是 node ，我们可以改为 jsdom ，不然访问不到例如 window 对象。
  // 表示它是一个类浏览器的测试环境，我们可以使用浏览器环境中的一些 API。
  "testEnvironment": "jsdom",

  // 要测试文件的正则表达式。
  // 表示 test 目录下所有以 .test.ts 和 .spec.ts 的文件都需要跑测试。
  "testRegex": "/test/.*\\.(test|spec)\\.(ts)$",

  // 模块文件扩展名，当你去引入一个模块并没有指定扩展名的时候，它会依次尝试去添加这些扩展名去找你引入的模块文件。
  // 表示优先找 .ts 的模块、然后是 .tsx，最后是 .js。
  "moduleFileExtensions": [
    "ts",
    "tsx",
    "js"
  ],

  // 测试覆盖率的阈值设定，当我们的测试覆盖率达不到阈值的时候，测试会失败。
  // 表示全局的代码分支覆盖率要达到 90%，方法覆盖率要达到 95%，代码行数覆盖率达到 95%，声明覆盖率达到 95%。
  "coverageThreshold": {
    "global": {
      "branches": 90,
      "functions": 95,
      "lines": 95,
      "statements": 95
    }
  },

  // 收集指定文件的测试覆盖率(即使你没为这些文件编写测试)，它的值为 glob patterns 类型。
  // 表示收集 src 目录以及它的所有子目录中的 js 和 ts 文件的测试覆盖率。
  "collectCoverageFrom": [
    "src/*.{js,ts}",
    "src/**/*.{js,ts}"
  ],
},
```
## 编写单元测试
在 test 目录下新建 index.spec.ts 测试文件，在下面输入我们的测试代码
```
import TsSdk from "../src/typescript-sdk"

/**
 * TsSdk test
 */
describe('TsSdk test', () => {
    test('should return a number muti value', () => {
      const a = 1
      const b = 3
  
      const result = TsSdk.add(a, b)
  
      expect(result).toBe(4)
    })
  })
```
执行 npm run test 查看我们的测试结果！
![截屏2021-06-02 下午1.55.25.png](https://upload-images.jianshu.io/upload_images/11846892-4a2a99faa3872ab1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于我们的功能函数较简单，所以覆盖率很简单就达到了百分之百。

⚠️注意: 在自己编写过程中，不是语法层面错误时，测试失败是因为测试覆盖率的阈值设定，当我们的测试覆盖率达不到阈值的时候，测试会失败。可以根据自己需要将阈值调低，或者尽可能编写完善的单元测试。

## 五、打包
---

我们会利用 `rollup` 来打包我们的 ts-sdk 库，它是一个非常著名的编译打包工具，相比 webpack，它非常适合去编译和打包一些 JS 库。

由于使用 `typescript-library-starter` 初始化我们的项目，我们已经拥有了 `rollup` 打包的相关配置和相关插件的安装，接下来我们就来对生成的 `rollup.config.ts` 做小小的梳理。

`rollup.config.ts`
```
import resolve from 'rollup-plugin-node-resolve'
import commonjs from 'rollup-plugin-commonjs'
import sourceMaps from 'rollup-plugin-sourcemaps'
import camelCase from 'lodash.camelcase'
import typescript from 'rollup-plugin-typescript2'
import json from 'rollup-plugin-json'

const pkg = require('./package.json')

const libraryName = 'typescript-sdk'

export default {
  // 表示打包入口文件。
  input: `src/${libraryName}.ts`,

  // 表示输出的目标文件，它是一个对象数组，我们可以指定输出的格式，比如 umd 格式、es 模式等。
  output: [
    { file: pkg.main, name: camelCase(libraryName), format: 'umd', sourcemap: true },
    { file: pkg.module, format: 'es', sourcemap: true },
  ],
  // Indicate here external modules you don't wanna include in your bundle (i.e.: 'lodash')
 
  // 声明它的外部依赖，可以不被打包进去。
  external: [],

  // 监听文件的变化，重新编译，只有在编译的时候开启 --watch 才生效。
  watch: {
    include: 'src/**',
  },

  // 编译过程中使用的插件，其中 rollup-plugin-typescript2 就是用来编译 TypeScript 文件，useTsconfigDeclarationDir 表示使用 tsconfig.json 文件中定义的 declarationDir。
  plugins: [
    // Allow json resolution
    json(),
    // Compile TypeScript files
    typescript({ useTsconfigDeclarationDir: true }),
    // Allow bundling cjs modules (unlike webpack, rollup doesn't understand cjs)
    commonjs(),
    // Allow node_modules resolution, so you can use 'external' to control
    // which external modules to include in the bundle
    // https://github.com/rollup/rollup-plugin-node-resolve#usage
    resolve(),

    // Resolve source maps to the original source
    sourceMaps(),
  ],
}

```
#### 修改 package.json
如果已经在 `rollup.config.ts` 中修改了 libraryName 为 ***， 那么在 `package.json` 文件中你需要做相关的修改：
```
{
  "main": "dist/***.umd.js",
  "module": "dist/***.es5.js",
  "typings": "dist/types/***.d.ts",
}
```
⚠️说明：在控制台执行 `npm run build`，会编译输出 dist 目录，其中 lib 目录是单个 .ts 文件编译后的 .js 文件。types 目录是所有 .ts 文件编译后生产的 .d.ts 声明文件。

 `***.es5.js`是编译后生成的 es 模式的入口文件，用在 package.json 的 module 字段，`***.umd.js` 文件是编译后生成的 umd 模式的入口文件，用在 package.json 的 main 字段。

`增加 2 个 npm scripts：`
```
{
  "prepub": "npm run test:prod && npm run build",
  "pub": "sh release.sh"
}
```
当我们运行 `npm run pub` 的时候，会优先执行 prepub 脚本，在 prepub 中我们运行了 test:prod 和 build 2 个脚本。&& 符号表示前面一个命令执行成功后才会执行后面的任务。

`npm run test:prod` 实际上运行了 npm run lint && npm run test -- --no-cache。 先运行 lint 去校验我们的源码和测试文件是否遵循 tslint 规范，再运行 test 去跑测试。

`npm run build` 实际上运行了 tsc --module commonjs、rollup -c rollup.config.ts 和 typedoc --out docs --target es6 --theme minimal --mode file src。先运行 tsc 去编译我们的 TypeScript 文件，dist/lib 和 dist/types 下的文件就是该命令产生的，然后运行 rollup 去构建 Add.umd.js 及 Add.es.js，最后运行 typedoc 去构建项目的文档。

运行完 prepub 后就会再运行 pub 命令，实际上执行了 sh release.sh 命令，但是目前我们没有这个脚本，接下来我们就需要来编写部署脚本 release.sh。

### 编写部署脚本
`release.sh：`
```
// 部署脚本是 shell 脚本，shell 脚本就是封装了多行控制台命令，来逐行解释他们的含义。

// 用来表示它是一个 shell 脚本。
#!/usr/bin/env sh

// 告诉脚本如果执行结果不为 true 则退出。
set -e

// 在控制台输出 Enter release version:。
echo "Enter release version: "

// 表示从标准输入读取值，并赋值给 $VERSION 变量。
read VERSION

// 其中 read -p 表示给出提示符
// -n 1 表示限定最多可以有 1 个字符可以作为有效读入
// -r 表示禁止反斜线的转义功能。因为我们的 read 并没有指定变量名，那么默认这个输入读取值会赋值给 $REPLY 变量。
read -p "Releasing $VERSION - are you sure? (y/n)" -n 1 -r

// echo 输出空值表示跳到一个新行，# 在 shell 脚本中表示注释。
echo  # (optional) move to a new line

// 表示 shell 脚本中的流程控制语句，判断 $REPLY 是不是大小写的 y，如果满足，则走到后面的 then 逻辑。
if [[ $REPLY =~ ^[Yy]$ ]]
then

// 在控制台输出 Releasing $VERSION ...。
  echo "Releasing $VERSION ..."
  # commit

// 表示把代码所有变化提交到暂存区。
  git add -A
  git commit -m "[build] $VERSION"

// 修改 package.json 中的 version 字段到 $VERSION，并且提交一条修改记录，提交注释是 [release] $VERSION。
  npm version $VERSION --message "[release] $VERSION"
// 把代码发布到主干分支
  git push origin master

//是把仓库发布到 npm 上，我们会把 dist 目录下的代码都发布到 npm 上，因为我们在 package.json 中配置的是 files 是 ["dist"]。
  # publish
  npm publish
fi
```

## 六、发布
---
发布之前，我们要对代码进行压缩，这样可以使打出的包更小。
安装 rollup-plugin-terser :
```
npm install rollup-plugin-terser -D
```
修改`rollup.config.ts`
```
//....
import { terser } from "rollup-plugin-terser"
//.....
export default {
  //....
  plugins: [
    //....
    terser()
  ],
}
```
## 运行部署脚本
接下来我们就运行 npm run pub 脚本部署，按提示直接来，就可以发包了！
注意了，npm必须切换为初始源！
### 扩展：错误处理
* 执行打包命令中的`tsc --module commonjs`命令，会报错
![截屏2021-06-03 上午9.49.25.png](https://upload-images.jianshu.io/upload_images/11846892-5d604d88915d1dd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`问题`❓**：在`tsconfig.json`中已经设置 exclude node_modules了，tsc 还是执行到 node_modules 里面去了？
**`原因❗️：`**exclude只是告诉Typescript这不是源代码，不要去转译和检查它。但是Typescript还是会去node_modules中查找第三方库的声明文件，这个行为也可以通过types或typesRoots选项配置。

![截屏2021-06-03 上午9.59.53.png](https://upload-images.jianshu.io/upload_images/11846892-a89ba2703a9e9631.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**`解决办法❗️：`**
```
// 注释
"typeRoots": [
    //   "node_modules/@types"
    ]
```
----
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接： 
* https://www.yuque.com/u188805/ewtgo7/lmvqwh?language=en-us
* https://www.tslang.cn/docs/handbook/tsconfig-json.html
