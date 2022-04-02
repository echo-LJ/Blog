---
title: 使用react搭建组件库--icon组件
date: 2020-04-01 10:00:00
tags: React
---
<meta name="referrer" content="no-referrer"/>

## 前言
你应该需要：[使用react搭建组件库：react+typescript+storybook]([https://www.jianshu.com/p/8ef96e06adcc](https://www.jianshu.com/p/8ef96e06adcc)
)
## 一、安装依赖
在组件中使用 `fortawesome`： [一套绝佳的图标字体库和CSS框架](http://www.fontawesome.com.cn)

```
npm install @fortawesome/fontawesome-svg-core
npm install @fortawesome/free-solid-svg-icons
npm install @fortawesome/react-fontawesome
```
## 二、编写组件
* 新建 `src/components/Icon/icon.tsx`
```
import React, { FC } from "react";
import classnames from "classnames";
import { library } from "@fortawesome/fontawesome-svg-core";
import { fas } from "@fortawesome/free-solid-svg-icons";

import {
  FontAwesomeIconProps,
  FontAwesomeIcon,
} from "@fortawesome/react-fontawesome";

library.add(fas);

export type theme = 
| "primary"
| "secondary"
| "success"
| "info"
| "warning"
| "danger"
| "light"
| "dark";

export interface IconProps extends FontAwesomeIconProps {
  /** 主题颜色 */
  theme?: theme;
}

export const Icon: FC<IconProps> = (props) => {
  const { className, theme, ...restProps } = props;
  const classes = classnames("lin-icon", className, {
    [`lin-icon-${theme}`]: theme,
  });

  return <FontAwesomeIcon className={classes} {...restProps} />;
};

export default Icon;
```
* 新建 src/components/Icon/index.tsx
 ```
import Icon from "./icon";

export default Icon;
```
* 新建 src/components/Icon/_style.scss
```
@import "../../styles/variables/icon";
// 循环
@each $key, $val in $icon-theme-color {
  .lin-icon-#{$key} {
    color: $val;
  }
}
```

* 新建 styles/variables/_icon.scss文件
```
@import "./common";

$icon-theme-color: (
  "primary": $primary,
  "secondary": $secondary,
  "success": $success,
  "info": $info,
  "warning": $warning,
  "danger": $danger,
  "light": $light,
  "dark": $dark,
);
```
* 新建 src/styles/index.scss文件
```
// icon样式
@import "../components/Icon/style";
```
* 修改App.tsx文件
！！ 对于`fortawesome`： [一套绝佳的图标字体库和CSS框架]中的图标都可以使用
```
// 增加
import Icon from "./components/Icon/icon";
<Icon icon="coffee" size="5x" theme="danger" />
<Icon icon="arrow-down" size="5x" />
```
* 修改index.tsx文件
```
export { default as Icon } from "./components/Icon";
```
## 三、运行项目
 执行命令
```
$ npm start
```
访问项目 可以看到Icon组件成功了！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-8c703c6cd972d3e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 四、编写storybook文档
* 新建`src/Icon/icon.stories.tsx`文件
```
import React from 'react'

import { storiesOf } from '@storybook/react'

import Icon from './icon'



const defaultIcon = () => {
    return (
        <div>
            <Icon
                icon="check"
                size="3x"
            />
            <Icon
                icon="times"
                size="3x"
                className='ml-20'
            />
            <Icon
                icon="anchor"
                size="3x"
                className='ml-20'
            />
            <Icon
                icon="trash"
                size="3x"
                className='ml-20'
            />
        </div>
    )
}

const iconWithTheme = () => {
    return (
        <div>
            <Icon
                icon="check"
                size="3x"
                theme="success"
            />
            <Icon
                icon="times"
                size="3x"
                theme="danger"
                className='ml-20'
            />
            <Icon
                icon="anchor"
                size="3x"
                theme="primary"
                className='ml-20'
            />
            <Icon
                icon="exclamation-circle"
                size="3x"
                theme="warning"
                className='ml-20'
            />
        </div>
    )
}

const iconWithOther = () => {
    return (
        <div>
            <Icon
                icon="spinner"
                size="3x"
                spin
                theme="primary"
            />
            <Icon
                icon="spinner"
                pulse
                size="3x"
                theme="success"
                className='ml-20'
            />
        </div>
    )
}


storiesOf('Icon 组件', module)
    .addParameters({
        info: {
            text: `
            ## 引用方法
            ~~~js
            import {Icon} from 'echo-rui
            ~~~
            `
        }
    })
    .add('默认的 Icon', defaultIcon)
    .add('不同主题的 Icon', iconWithTheme)
    .add('更多行为的 Icon', iconWithOther,{
        info: {
            text: `
            ## 引用方法
            ~~~js
            import {Icon} from 'echo-rui
            ~~~
            更多例子请参见：https://github.com/FortAwesome/react-fontawesome#basic
            `
        }
    })
```
* 执行命令
```
$ npm run storybook
```
浏览器打开http://localhost:9009/,可以看到组件库文档生成了。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-f5e3bf4ef4168c78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 五、 发布到npm
 ✅ ✅ ✅ ✅ ✅
----
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


