---
title: 使用react搭建组件库：react+typescript+storybook
date: 2020-06-08 21:12:16
tags: React
---
<meta name="referrer" content="no-referrer"/>

## 一、 安装组件库
执行安装命令
 ```
npx create-react-app echo-rui  --typescript
```
## 二、 组件库配置eslint
* 配置ESlint
新建`.eslintrc.json`文件
```
{
  "extends": "react-app"
}
```
新建 `.vscode/settings.json`文件
```
{
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    { "language": "typescript", "autoFix": true },
    { "language": "typescriptreact", "autoFix": true }
  ]
}
```
## 三、 引入依赖
在组件中使用 `classname`：[https://github.com/jedWatson/classnames](https://github.com/jedWatson/classnames)
执行安装命令
```
npm install classnames -D
npm install @types/classnames -D
npm install node-sass -D
```
使用方法示例：
如果对象的key值是变化的，可以采用下面的中括号的形式：[`btn-${btnType}`]
```
// btn, btn-lg, btn-primary
const classes = classNames('btn', className, {
   [`btn-${btnType}`]: btnType,
   [`btn-${size}`]: size,
   'disabled': (btnType === 'link') && disabled
})
```
## 四、 编写组件
* 新建 `src/components/Button/button.tsx`

```
import React,{FC,ButtonHTMLAttributes,AnchorHTMLAttributes} from "react";
import classnames from "classnames";


// 按钮大小
export type ButtonSize = "lg" | "sm";

export type ButtonType = "primary" | "default" | "danger" | "link";

interface BaseButtonProps {
  /** 自定义类名 */
  className?: string;
  /** 设置Button 的禁用 */
  disabled?: boolean;
  /** 设置Button 的大小 */
  size?: ButtonSize;
  /** 设置Button 的类型 */
  btnType?: ButtonType;
  children: React.ReactNode;
  /** 当btnType为link时，必填 */
  href?: string;
}

// 并集
type NativeButtonProps = BaseButtonProps &
  ButtonHTMLAttributes<HTMLElement>;
type AnchorButtonProps = BaseButtonProps &
  AnchorHTMLAttributes<HTMLElement>;

// Partial：typescript全局函数，将属性全部变成可选的
export type ButtonProps = Partial<NativeButtonProps & AnchorButtonProps>;

// 使用react-docgen-typescript-loader的bug，只能使用FC，不能React.FC
export const Button: FC<ButtonProps> = (props) => {
  const {
    disabled,
    size,
    btnType,
    children,
    href,
    className,
    ...resetProps
  } = props;

  const classes = classnames("echo-btn", className, {
    [`echo-btn-${btnType}`]: btnType,
    [`echo-btn-${size}`]: size,
    "echo-button-disabled": btnType === "link" && disabled,
  });
  if (btnType === "link" && href) {
    return (
      <a href={href} className={classes} {...resetProps}>
        {children}
      </a>
    );
  } else {
    return (
      <button className={classes} disabled={disabled} {...resetProps}>
        {children}
      </button>
    );
  }
};

Button.defaultProps = {
  disabled: false,
  btnType: "default",
};

export default Button;



```
* 新建 `src/components/Button/index.tsx`
```
import Button from "./button";

export default Button;
```
* 新建 `src/components/Button/_style.scss`
```
@import "../../styles/variables/button";
@import "../../styles/mixin/button";
.echo-btn {
  position: relative;
  display: inline-block;
  font-weight: $btn-font-weight;
  line-height: $btn-line-height;
  color: $body-color;
  white-space: nowrap;
  text-align: center;
  vertical-align: middle;
  background-image: none;
  border: $btn-border-width solid transparent;
  @include button-size(
    $btn-padding-y,
    $btn-padding-x,
    $btn-font-size,
    $btn-border-radius
  );
  box-shadow: $btn-box-shadow;
  cursor: pointer;
  transition: $btn-transition;
  &.echo-button-disabled
  &[disabled] {
    cursor: not-allowed;
    opacity: $btn-disabled-opacity;
    box-shadow: none;
    > * {
      pointer-events: none;
    }
  }
}

.echo-btn-lg {
  @include button-size(
    $btn-padding-y-lg,
    $btn-padding-x-lg,
    $btn-font-size-lg,
    $btn-border-radius-lg
  );
}

.echo-btn-sm {
  @include button-size(
    $btn-padding-y-sm,
    $btn-padding-x-sm,
    $btn-font-size-sm,
    $btn-border-radius-sm
  );
}

.echo-btn-primary {
  @include button-style($primary, $primary, $white);
}

.echo-btn-danger {
  @include button-style($danger, $danger, $white);
}

.echo-btn-default {
  @include button-style(
    $white,
    $gray-400,
    $body-color,
    $white,
    $primary,
    $primary
  );
}

.echo-btn-link {
  font-weight: $font-weight-normal;
  color: $btn-link-color;
  text-decoration: $link-decoration;
  box-shadow: none;
  &:hover {
    color: $btn-link-hover-color;
    text-decoration: $link-hover-decoration;
  }
  &:focus {
    text-decoration: $link-hover-decoration;
    box-shadow: none;
  }
  &:disabled,
  &.echo-button-disabled {
    color: $btn-link-disabled-color;
    pointer-events: none;
  }
}
```
* 新建 `styles/variables/button.scss`文件
```
@import "./common";
// 按钮基本属性
$btn-font-weight: 400;
$btn-padding-y: 0.375rem !default;
$btn-padding-x: 0.75rem !default;
$btn-font-family: $font-family-base !default;
$btn-font-size: $font-size-base !default;
$btn-line-height: $line-height-base !default;
...
...
```
* 新建 styles/mixin/button.scss`文件
```
@mixin button-size($padding-y, $padding-x, $font-size, $border-raduis) {
  padding: $padding-y $padding-x;
  font-size: $font-size;
  border-radius: $border-raduis;
}

@mixin button-style(
  $background,
  $border,
  $color,
  // lghten,sass内置函数，比$background颜色要浅上7.5%
    $hover-background: lighten($background, 7.5%),
  $hover-border: lighten($border, 10%),
  $hover-color: $color
) 
...
...
```
* 在 `src/styles/index.scss`文件中引入组件样式
```
// index文件主要是引入所有组件的样式。
// 不需要写_，这是sass的一种写法，告诉sass这些样式不打包到css中，只能做导入，也是一种模块化

// 按钮样式
@import "../components/Button/style";

```
* 新建 `src/styles/index.scss`文件
```
// index文件主要是引入所有组件的样式。
// 不需要写_，这是sass的一种写法，告诉sass这些样式不打包到css中，只能做导入，也是一种模块化

// 按钮样式
@import "../components/Button/style";
...

```
## 五、 删除多余文件+引用组件
* 删除`src/App.css` + `src/logo.svg` + `src/index.css` + `src/App.test.js` + `serviceWorker.ts`文件
* 修改`App.tsx`文件
```
import React from "react";
import "./styles/index.scss";
import Button, { ButtonType, ButtonSize } from "./components/Button/button";

function App() {
  return (
    <div className="App">
      <Button>hello</Button>
      <Button disabled>hello</Button>
      <Button btnType="primary" size="sm">
        hello
      </Button>
      <Button
        btnType="danger"
        size="lg"
        onClick={() => {
          alert(111);
        }}
      >
        hello
      </Button>
     <Button
        btnType="link"
        href="http://www.baidu.com"
        target="_blank"
      >
        hello
      </Button>
      <Button disabled btnType="link" href="http://www.baidu.com">
        hello
      </Button>
    </div>
  );
}
export default App;
```
* 修改`src/index.tsx`文件
```
export { default as Button } from "./components/Button";
```
## 六、 运行项目
执行命令
```
npm start
```
访问项目 可以看到button组件成功了！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8690603021ebd620.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 七、单元测试
新建`src/Button/button.test.tsx`文件
```
import React from "react";
import { render, fireEvent } from "@testing-library/react";
import Button, { ButtonProps } from "./button";

describe("Button 组件", () => {
  it('默认Button', () => {
    const testProps: ButtonProps = {
      onClick: jest.fn(),
    }
    const wrapper = render(<Button {...testProps}>hello</Button>);
    const element = wrapper.getByText('hello')as HTMLButtonElement;
    // 元素是否被渲染在文档中
    expect(element).toBeInTheDocument();
    // 判断标签名
    expect(element.tagName).toEqual("BUTTON");
     // 判断是否有类名
     expect(element).toHaveClass("echo-btn-default");
     expect(element).not.toHaveClass("echo-disabled");
     //   触发点击事件
    fireEvent.click(element);
    expect(testProps.onClick).toHaveBeenCalled();
    expect(element.disabled).toBeFalsy();
  })
  it("测试传入不同属性的情况", () => {
    const testProps: ButtonProps = {
      btnType: "primary",
      size: "lg",
      className: "test-name",
    };
    const wrapper = render(<Button {...testProps}>hello</Button>);
    const element = wrapper.getByText("hello") as HTMLButtonElement;
    expect(element).toBeInTheDocument();
    expect(element).toHaveClass("echo-btn-primary");
    expect(element).toHaveClass("echo-btn-lg");
    expect(element).toHaveClass("test-name");
  });

  it("测试当btnType为link和href存在的情况", () => {
    const testProps: ButtonProps = {
      btnType: "link",
      href: "http://www.baidu.com",
    };
    const wrapper = render(<Button {...testProps}>Link</Button>);
    const element = wrapper.getByText("Link") as HTMLAnchorElement;
    expect(element).toBeInTheDocument();
    expect(element.tagName).toEqual("A");
    expect(element).toHaveClass("echo-btn-link");
  });

  it("测试禁用的情况", () => {
    const testProps: ButtonProps = {
      onClick: jest.fn(),
      disabled: true,
    };
    const wrapper = render(<Button {...testProps}>Disabled</Button>);
    const element = wrapper.getByText("Disabled") as HTMLButtonElement;
    expect(element).toBeInTheDocument();
    expect(element.disabled).toBeTruthy();
    fireEvent.click(element);
    expect(testProps.onClick).not.toHaveBeenCalled();
  });
});
```
执行命令
```
npm test
```
可以看到单元测试成功通过！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-04f6282ff0e82886.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 八、组件库实现按需加载
* 安装依赖
```
$npm install node-cmd -D
```
* 新建 `buildScss.js`文件
```
const cmd = require("node-cmd");
const path = require("path");
const fs = require("fs");
const entryDir = path.resolve(__dirname, "./src/components");
const outputDir = path.resolve(__dirname, "./dist/components");
function getScssEntry() {
  let entryMap = {};
  fs.readdirSync(entryDir).forEach(function (pathName) {
    const entryName = path.resolve(entryDir, pathName);
    const outputName = path.resolve(outputDir, pathName);
    let entryFileName = path.resolve(entryName, "_style.scss");
    let outputFileName = path.resolve(outputName, "style/index.css");

    entryMap[pathName] = {};
    entryMap[pathName].entry = entryFileName;
    entryMap[pathName].output = outputFileName;
  });

  return entryMap;
}
const entry = getScssEntry();
let buildArr = [];
for (const key in entry) {
  const promise = new Promise((resolve, reject) => {
    cmd.get(`npx node-sass ${entry[key].entry} ${entry[key].output}`, function (
      err,
      data,
      stderr
    ) {
      if (err) {
        reject(err);
        return;
      }
      console.log("the current working dir is : ", data);
      fs.writeFileSync(
        path.join(__dirname, `./dist/components/${key}/style/css.js`),
        "import './index.css'"
      );
      resolve();
    });
  });
  buildArr.push(promise);
}

Promise.all(buildArr)
  .then(() => {
    console.log("build success");
  })
  .catch((e) => {
    console.log(e);
  });


```
* 新建 `_babel.config.js`文件
libraryName:需要加载的库，我这里的是echo-rui
libraryDirectory：需要加载的组件库所在的目录，当前的组件是存放在dist/components下的
style：加载的css类型，当前项目只有css，没有less，所以这里写css即可，如需配置less,请看注释
```
module.exports = {
  presets: ["react-app"],
  plugins: [
    [
      "import",
      {
        libraryName: "echo-rui", 
        camel2DashComponentName: false, // 是否需要驼峰转短线
        camel2UnderlineComponentName: false, // 是否需要驼峰转下划线
        libraryDirectory: "dist/components",
        style: "css",
      },
    ],
    // ["import", {
    //   "libraryName": "antd",
    //   "libraryDirectory": "es",
    //   "style": "css" // `style: true` 会加载 less 文件
    // }]
  ],
};

```
⚠️⚠️⚠️重要说明：在开发的时候，每个组件目录下面必须有一个`_style.scss`样式文件，即使他是个空文件也必须有，否则会在按需引入的时候报错找不到css文件⚠️⚠️

## 九、storybook文档生成
1. 初始化storyBook
```
npx -p @storybook/cli sb init
```
2. 添加依赖和插件
```
npm install @storybook/addon-info --save-dev
```
3. 添加npm脚本
```
 "scripts": {
    ...
    "storybook": "start-storybook -p 9009 -s public",
    "build-storybook": "build-storybook -s public"
  },
```
4. 配置storybook，支持typescript
```
npm install react-docgen-typescript-loader -D
```
5. 添加storybook配置文件
* 新建`.storybook/webpack.config.js`文件
`shouldExtractLiteralValuesFromEnum`：storybook爬取组件属性的时候会自动把type类型的属性自动展开。
 `propFilter`：过滤掉不需要爬取的属性的来源。
```
module.exports = ({ config }) => {
  config.module.rules.push({
    test: /\.tsx?$/,
    use: [
      {
        loader: require.resolve("babel-loader"),
        options: {
          presets: [require.resolve("babel-preset-react-app")]
        }
      }, 
      {
        loader: require.resolve("react-docgen-typescript-loader"),
        options: {
          shouldExtractLiteralValuesFromEnum: true,
          propFilter: (prop) => {
            if (prop.parent) {
              return !prop.parent.fileName.includes('node_modules')
            }
            return true            
          }
        }
      }
    ]
  });

  config.resolve.extensions.push(".ts", ".tsx");

  return config;
};
```
* 新建`.storybook/style.scss`文件
```
// 这个文件主要是对storybook文档样式的配置
```
* 新建`.storybook/config.tsx`文件
配置插件以及需要加载的文件
```
import { configure,addDecorator,addParameters } from '@storybook/react';
import { withInfo } from '@storybook/addon-info'


import '../src/styles/index.scss'
import './style.scss'
import React from 'react'

const wrapperStyle: React.CSSProperties = {
  padding: '20px 40px'
}

const storyWrapper = (stroyFn: any) => (
  <div style={wrapperStyle}>
    <h3>组件演示</h3>
    {stroyFn()}
  </div>
)
addDecorator(storyWrapper)
addDecorator(withInfo)
addParameters({info: { inline: true, header: false}})


const loaderFn = () => {
    const allExports = [];
    const req = require.context('../src/components', true, /\.stories\.tsx$/);
    req.keys().forEach(fname => allExports.push(req(fname)));
    return allExports;
  };

configure(loaderFn, module);

```

6. 在每个组件目录下面新建一个`.stories.tsx`结尾的文件
* button组件下新建`button.stories.tsx`文件
```
import React from 'react';
import { storiesOf } from '@storybook/react';
import { action } from '@storybook/addon-actions';

import Button from './button'

const defaultButton = () => (
    <div>
        <Button onClick={action('default button')}>default button</Button>
    </div>
)

const buttonWithSize = () => (
    <div>
        <Button size='lg' btnType='primary' onClick={action('lg button')}>lg button</Button>
        <Button className='ml-20' size='sm' btnType='danger' onClick={action('sm button')}>sm button</Button>
    </div>
)


const buttonWithType = () => (
    <div>
        <Button onClick={action('danger button')} btnType='danger'>danger button</Button>
        <Button onClick={action('primary button')} className='ml-20' btnType='primary'>primary button</Button>
        <Button onClick={action('link')} className='ml-20' btnType='link' href='https://www.baidu.com/'>link</Button>
    </div>
)

const buttonWithDisabled = () => (
    <div>
        <Button onClick={action('disabled button')} btnType='danger' disabled={true}>disabled button</Button>
        <Button onClick={action('unDisabled button')} className='ml-20' btnType='primary'>unDisabled button</Button>
    </div>
)

// storiesOf('Button组件', module)
// .addDecorator(withInfo)
// .addParameters({
//     info:{
//         text:`
//         这是默认组件
//         ~~~js
//         const a = 12
//         ~~~
//         `,
//         inline:true
//     }
// })
//   .add('默认 Button', defaultButton)
//   .add('不同尺寸 Button', buttonWithSize,{info:{inline:false}})
//   .add('不同类型 Button', buttonWithType)

storiesOf('Button 按钮', module)
    .addParameters({
        info: {
            text: `
        ## 引用方法
        ~~~js
        import {Button} from ecoh-rui
        ~~~
        `
        }
    })
    .add('默认 Button', defaultButton)
    .add('不同尺寸 Button', buttonWithSize)
    .add('不同类型 Button', buttonWithType)
    .add('禁用的 Button',buttonWithDisabled)

```
7.执行命令
```
npm run storybook
```
在终端可以看到
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8b43e51f15d70e6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
浏览器打开`http://localhost:9009/`,可以看到组件库文档生成了。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-151907d5b1ed9637.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 十、 typescript编译配置
新建`tsconfig.build.json`文件
```

{
  "compilerOptions": {
    // 输出路径
    "outDir": "dist",
    // 打包模块规范
    "module": "esnext",
    // 构建目标
    "target": "es5",
    // 生成定义文件d.ts
    "declaration": true,
    "jsx": "react",
    // 模块引入策略
    "moduleResolution": "Node",
    // 允许import React from 'react'这样导包
    "allowSyntheticDefaultImports": true
  },
  // 需要编译的目录
  "include": ["src"],
  // 不需要编译的
  "exclude": ["src/**/*.test.tsx", "src/**/*.stories.tsx", "src/setupTests.ts"]
}

```
## 十一、 package.json相关配置
* 将依赖包从dependencies搬到devDependencie
>为什么要这么做？
一、是防止发布组件库之后别人使用了跟我们不一样的react版本造成冲突
二、是我们在开发的时候还需要使用到react和react-dom，所以不能删除，只能搬到devDependencies
三、还有一些跟发布后的组件库不相关的依赖都需要搬到devDependencies，例如storybook等
```
// 嘿嘿，移动之后，发现为空了，重新install 了一下
// 运行了npm start 和npm run test 和npm run storybook发现一切正常，开心一下！！
"dependencies": {},
```
* 添加npm发布相关配置
```
"description": "react components library",
"author": "echo",
"private": false,
// 主入口
"main": "dist/index.js",
// 模块入口
"module": "dist/index.js",
// 类型文件声明
"types": "dist/index.d.ts",
"license": "MIT",
// 关键词
"keywords": [
    "React",
    "UI",
    "Component",
    "typescript"
  ],
// 首页你的github地址
  "homepage": "https://github.com/.../echo-rui",
// 仓库地址
  "repository": {
    "type": "git",
    "url": "https://github.com/.../echo-ruii"
  },
  // 需要上传的文件，不写就默认以.gitignore为准
  "files": [
    "dist"
  ],
```
* 代码提交git前检查。这里使用husky这个工具
```
"husky": {
    "hooks": {
    // 用git提交代码之前
      "pre-commit": "npm run test:nowatch && npm run lint"
    }
  },
"scripts": {
   ...
    "lint": "eslint --ext js,ts,tsx src --fix --max-warnings 5",
    "test:nowatch": "cross-env CI=true react-scripts test",
   ... 
```
* 添加打包发布相关配置
```
"scripts": {
   ...
    "clean": "rimraf ./dist",
    "build-ts": "tsc -p tsconfig.build.json",
    "build-css": "node-sass ./src/styles/index.scss ./dist/index.css",
    "build": "npm run clean && npm run build-ts && npm run build-css && node ./buildScss.js",
    "prepublishOnly": "npm run lint && npm run build"
  
   ... 
```
此时执行打包命令，就可以成功根据配置打包了。
## 十二、 发布到npm
万事俱备，只欠发布。
* 完善一下 README.md 文档，这个随便写两句就好
* 在根目录下新建一个 .npmignore 文件，内容和 .gitignore 差不多
最后执行 npm login 登入 npm 账号，再执行 npm publish 发布即可，就这么简单的两步就可以，过一会在 npm 上就能搜到了。当然前提是你有个 npm 账号，没有的话去注册一个吧，很 easy 的，然后还要搜下你的 npm 包名是否有人用，有的话就换一个。

大功告成✌️✌️✌️