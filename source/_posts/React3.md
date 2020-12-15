---
title: 使用react搭建组件库--radio组件
date: 2020-05-01 10:00:00
tags: React
---
<meta name="referrer" content="no-referrer"/>

## 前言
你应该需要：[使用react搭建组件库：react+typescript+storybook]([https://www.jianshu.com/p/8ef96e06adcc](https://www.jianshu.com/p/8ef96e06adcc)
)
## 一、 安装依赖
在组件中使用 `rc-checkbox`： [react rc-checkbox component](https://www.npmjs.com/package/rc-checkbox)

```
npm install rc-checkbox
```
## 二、 编写组件
* 新建 `src/components/Radio/radio.tsx`
```
import * as React from 'react';
import RcCheckbox from 'rc-checkbox';
import classNames from 'classnames';
import { RadioProps, RadioChangeEvent } from './interface';
import { ConfigContext } from '../config-provider';
import RadioGroupContext from './context';
import { composeRef } from '../_util/ref';
const InternalRadio: React.ForwardRefRenderFunction<unknown, RadioProps> = (props, ref) => {
  const context = React.useContext(RadioGroupContext);
  const { getPrefixCls, direction } = React.useContext(ConfigContext);
  const innerRef = React.useRef<HTMLElement>();
  const mergedRef = composeRef(ref, innerRef);
  
  const onChange = (e: RadioChangeEvent) => {
    if (props.onChange) {
      props.onChange(e);
    }
    if (context?.onChange) {
      context.onChange(e);
    }
  };

  const { prefixCls: customizePrefixCls, className, children, style, ...restProps } = props;
  const prefixCls = getPrefixCls('radio', customizePrefixCls);
  console.log(prefixCls, customizePrefixCls)
  const radioProps: RadioProps = { ...restProps };
  if (context) {
    radioProps.name = context.name;
    radioProps.onChange = onChange;
    radioProps.checked = props.value === context.value;
    radioProps.disabled = props.disabled || context.disabled;
  }
  const wrapperClassString = classNames(className, {
    [`${prefixCls}-wrapper`]: true,
    [`${prefixCls}-wrapper-checked`]: radioProps.checked,
    [`${prefixCls}-wrapper-disabled`]: radioProps.disabled,
    [`${prefixCls}-wrapper-rtl`]: direction === 'rtl',
  });
  return (
    <label
      className={wrapperClassString}
      style={style}
      onMouseEnter={props.onMouseEnter}
      onMouseLeave={props.onMouseLeave}
    >
      <RcCheckbox {...radioProps} prefixCls={prefixCls as string} ref={mergedRef as any} />
      {children !== undefined ? <span>{children}</span> : null}
    </label>
  );
}
const Radio = React.forwardRef<unknown, RadioProps>(InternalRadio);
Radio.displayName = 'Radio';

Radio.defaultProps = {
  type: 'radio',
};

export default Radio;
```
* 新建 src/components/Radio/interface.tsx
```
export interface AbstractCheckboxProps<T> {
  prefixCls?: string;
  className?: string;
  defaultChecked?: boolean;
  checked?: boolean;
  style?: React.CSSProperties;
  disabled?: boolean;
  onChange?: (e: T) => void;
  onClick?: React.MouseEventHandler<HTMLElement>;
  onMouseEnter?: React.MouseEventHandler<HTMLElement>;
  onMouseLeave?: React.MouseEventHandler<HTMLElement>;
  onKeyPress?: React.KeyboardEventHandler<HTMLElement>;
  onKeyDown?: React.KeyboardEventHandler<HTMLElement>;
  value?: any;
  tabIndex?: number;
  name?: string;
  children?: React.ReactNode;
  id?: string;
  autoFocus?: boolean;
  type?: string;
}
export type RadioProps = AbstractCheckboxProps<RadioChangeEvent>;
export interface RadioChangeEventTarget extends RadioProps {
  checked: boolean;
}
export interface RadioChangeEvent {
  target: RadioChangeEventTarget;
  stopPropagation: () => void;
  preventDefault: () => void;
  nativeEvent: MouseEvent;
}

export interface RadioGroupContextProps {
  onChange: (e: RadioChangeEvent) => void;
  value: any;
  disabled?: boolean;
  name?: string;
}
```
* 新建 src/components/config-provider/context.tsx: 为组件库提供共享状态
```
import * as React from 'react';
export interface ConfigConsumerProps {
  getPrefixCls: (suffixCls: string, customizePrefixCls?: string) => string;
  direction?: 'ltr' | 'rtl';
}
export const ConfigContext = React.createContext<ConfigConsumerProps>({
  // We provide a default function for Context without provider
  getPrefixCls: (suffixCls: string, customizePrefixCls?: string) => {
    if (customizePrefixCls) return customizePrefixCls;

    return suffixCls ? `echo-${suffixCls}` : 'echo';
  },
});

export const ConfigConsumer = ConfigContext.Consumer;
```

* 新建 src/components/config-provider/index.tsx: 为组件库提供共享状态
```
import { ConfigContext, ConfigConsumer } from './context';
export { ConfigContext, ConfigConsumer};
```
* 新建 src/components/config-provider/SizeContext.tsx: 为组件库提供大小共享状态
```
import * as React from 'react';
export type SizeType = 'small' | 'middle' | 'large' | undefined;
const SizeContext = React.createContext<SizeType>(undefined);
export default SizeContext;
```
* 新建 src/components/Radio/context.tsx: 为Radio相关组件提供共享状态
```
import * as React from 'react';
import { RadioGroupContextProps } from './interface';
const RadioGroupContext = React.createContext<RadioGroupContextProps | null>(null);
export const RadioGroupContextProvider = RadioGroupContext.Provider;
export default RadioGroupContext;
```
* 新建 src/components/_util/ref.tsx:组件库通用方法
```
import * as React from 'react';

export function fillRef<T>(ref: React.Ref<T>, node: T) {
  if (typeof ref === 'function') {
    ref(node);
  } else if (typeof ref === 'object' && ref && 'current' in ref) {
    (ref as any).current = node;
  }
}

export function composeRef<T>(...refs: React.Ref<T>[]): React.Ref<T> {
  return (node: T) => {
    refs.forEach(ref => {
      fillRef(ref, node);
    });
  };
}
```
* 新建 src/components/Radio/index.tsx

 ```
import Radio from "./radio";
export default Radio;
```
* 新建 src/components/Radio/_rtl.scss
```
@import "../../styles/variables/radio";


$radio-prefix-cls: 'echo-radio';
$radio-group-prefix-cls: '#{$radio-prefix-cls}-group';
$radio-prefix-cls-button-wrapper: '#{$radio-prefix-cls}-button-wrapper';
.#{$radio-group-prefix-cls}{
  &-rtl {
    direction: rtl;
  }
}

// 一般状态
.#{$radio-prefix-cls}-wrapper {
  &-rtl {
    margin-right: 0;
    margin-left: $radio-wrapper-margin-right;
    direction: rtl;
  }
}

.#{$radio-prefix-cls-button-wrapper} {
  &-rtl {
    border-right-width: 0;
    border-left-width: $border-width-base;
  }

  &:not(:first-child) {
    &::before {
      .#{$radio-prefix-cls-button-wrapper}.#{$radio-prefix-cls-button-wrapper}-rtl & {
        right: -1px;
        left: 0;
      }
    }
  }

  &:first-child {
    .#{$radio-prefix-cls-button-wrapper}.#{$radio-prefix-cls-button-wrapper}-rtl & {
      border-right: $border-width-base $border-style-base $border-color-base;
      border-radius: 0 $border-radius-base $border-radius-base 0;
    }
    .#{$radio-prefix-cls-button-wrapper}-checked:not([class*="'#{$radio-prefix-cls}-button-wrapper-disabled'"]) & {
      border-right-color:lighten($primary, 10%);
    }
  }

  &:last-child {
    .#{$radio-prefix-cls-button-wrapper}.#{$radio-prefix-cls-button-wrapper}-rtl & {
      border-radius: $border-radius-base 0 0 $border-radius-base;
    }
  }

  &-disabled {
    &:first-child {
      .#{$radio-prefix-cls-button-wrapper}.#{$radio-prefix-cls-button-wrapper}-rtl & {
        border-right-color: $border-color-base;
      }
    }
  }
}


```
* 新建 src/components/Icon/_style.scss
```
@import "../../styles/variables/radio";
@import "./rtl";

$radio-prefix-cls: 'echo-radio';
$radio-group-prefix-cls: '#{$radio-prefix-cls}-group';
$radio-inner-prefix-cls: '#{radio-prefix-cls}-inner';
$radio-duration: 0.3s;
$radio-focus-shadow: 0 0 0 3px fade($primary, 8%);
$radio-button-focus-shadow: $radio-focus-shadow;

.#{$radio-group-prefix-cls} {
  @include reset-component;

  display: inline-block;
  font-size: 0;
  line-height: unset;

  .echo-badge-count {
    z-index: 1;
  }

  .echo-badge:not(:first-child) .#{$radio-prefix-cls}-button-wrapper {
    border-left: none;
  }
}

// 一般状态
.#{$radio-prefix-cls}-wrapper {
  @include reset-component;

  position: relative;
  display: inline-block;
  margin-right: $radio-wrapper-margin-right;
  white-space: nowrap;
  cursor: pointer;
}

.#{$radio-prefix-cls} {
  @include reset-component;

  position: relative;
  top: -3px;
  display: inline-block;
  line-height: 1;
  white-space: nowrap;
  vertical-align: sub;
  outline: none;
  cursor: pointer;

  .#{$radio-prefix-cls}-wrapper:hover &,
  &:hover .#{$radio-inner-prefix-cls},
  &-input:focus + .#{$radio-inner-prefix-cls} {
    border-color: $radio-dot-color;
  }

  &-input:focus + .#{$radio-inner-prefix-cls} {
    box-shadow: $radio-focus-shadow;
  }

  &-checked::after {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: 1px solid $radio-dot-color;
    border-radius: 50%;
    visibility: hidden;
    animation: antRadioEffect 0.36s ease-in-out;
    animation-fill-mode: both;
    content: '';
  }

  &:hover::after,
  .#{$radio-prefix-cls}-wrapper:hover &::after {
    visibility: visible;
  }

  &-inner {
    &::after {
      $radio-dot-size: $radio-size - 8px;

      position: absolute;
      top: ($radio-size - $radio-dot-size) / 2 - 1px;
      left: ($radio-size - $radio-dot-size) / 2 - 1px;
      display: table;
      width: $radio-dot-size;
      height: $radio-dot-size;
      background-color: $radio-dot-color;
      border-top: 0;
      border-left: 0;
      border-radius: $radio-dot-size;
      transform: scale(0);
      opacity: 0;
      transition: all $radio-duration $ease-in-out-circ;
      content: ' ';
    }
  }
}

// 选中状态
.#{$radio-prefix-cls}-checked {
  .#{$radio-inner-prefix-cls} {
    border-color: $radio-dot-color;
    &::after {
      transform: scale(1);
      opacity: 1;
      transition: all $radio-duration $ease-in-out-circ;
    }
  }
}

.#{$radio-prefix-cls}-disabled {
  .#{$radio-inner-prefix-cls} {
    background-color: $disabled-color;
    border-color: $border-color-base !important;
    cursor: not-allowed;
    &::after {
      background-color: $radio-dot-disabled-color;
    }
  }

  .#{$radio-prefix-cls}-input {
    cursor: not-allowed;
  }

  & + span {
    color: $disabled-color;
    cursor: not-allowed;
  }
}

span.#{$radio-prefix-cls} + * {
  padding-right: 8px;
  padding-left: 4px;
}

.#{$radio-prefix-cls}-button-wrapper {
  position: relative;
  display: inline-block;
  height: $height-base;
  margin: 0;
  padding: 0 $padding-md - 1px;
  color: $radio-button-color;
  font-size: $font-size-base;
  line-height: $height-base - 2px;
  background: $radio-button-bg;
  border: $border-width-base $border-style-base $border-color-base;
  // strange align fix for chrome but works
  // https://gw.alipayobjects.com/zos/rmsportal/VFTfKXJuogBAXcvfAUWJ.gif
  border-top-width: $border-width-base + 0.02px;
  border-left-width: 0;
  cursor: pointer;
  transition: color 0.3s, background 0.3s, border-color 0.3s, box-shadow 0.3s;

  a {
    color: $radio-button-color;
  }

  > .#{$radio-prefix-cls}-button {
    display: block;
    width: 0;
    height: 0;
    margin-left: 0;
  }

  .#{$radio-group-prefix-cls}-large & {
    height: $height-lg;
    font-size: $font-size-lg;
    line-height: $height-lg - 2px;
  }

  .#{$radio-group-prefix-cls}-small & {
    height: $height-sm;
    padding: 0 $control-padding-horizontal-sm - 1px;
    line-height: $height-sm - 2px;
  }

  &:not(:first-child) {
    &::before {
      position: absolute;
      top: $border-width-base * -1;
      left: -1px;
      display: block;
      box-sizing: content-box;
      width: 1px;
      height: 100%;
      padding: $border-width-base 0;
      background-color: $border-color-base;
      transition: background-color 0.3s;
      content: '';
    }
  }

  &:first-child {
    border-left: $border-width-base $border-style-base $border-color-base;
    border-radius: $border-radius-base 0 0 $border-radius-base;
  }

  &:last-child {
    border-radius: 0 $border-radius-base $border-radius-base 0;
  }

  &:first-child:last-child {
    border-radius: $border-radius-base;
  }

  &:hover {
    position: relative;
    color: $radio-dot-color;
  }

  &:focus-within {
    box-shadow: $radio-button-focus-shadow;
  }

  .#{$radio-prefix-cls}-inner,
  input[type='checkbox'],
  input[type='radio'] {
    width: 0;
    height: 0;
    opacity: 0;
    pointer-events: none;
  }

  &-checked:not(&-disabled) {
    z-index: 1;
    color: $radio-dot-color;
    background: $radio-button-checked-bg;
    border-color: $radio-dot-color;

    &::before {
      background-color: $radio-dot-color;
    }

    &:first-child {
      border-color: $radio-dot-color;
    }

    &:hover {
      color: $radio-button-hover-color;
      border-color: $radio-button-hover-color;
      &::before {
        background-color: $radio-button-hover-color;
      }
    }

    &:active {
      color: $radio-button-active-color;
      border-color: $radio-button-active-color;
      &::before {
        background-color: $radio-button-active-color;
      }
    }

    &:focus-within {
      box-shadow: $radio-button-focus-shadow;
    }
  }

  .#{$radio-group-prefix-cls}-solid &-checked:not(&-disabled) {
    color: $radio-solid-checked-color;
    background: $radio-dot-color;
    border-color: $radio-dot-color;
    &:hover {
      color: $radio-solid-checked-color;
      background: $radio-button-hover-color;
      border-color: $radio-button-hover-color;
    }
    &:active {
      color: $radio-solid-checked-color;
      background: $radio-button-active-color;
      border-color: $radio-button-active-color;
    }
    &:focus-within {
      box-shadow: $radio-button-focus-shadow;
    }
  }

  &-disabled {
    color: $disabled-color;
    background-color: $disabled-bg;;
    border-color: $border-color-base;
    cursor: not-allowed;

    &:first-child,
    &:hover {
      color: $disabled-color;
      background-color: $disabled-bg;;
      border-color: $border-color-base;
    }
    &:first-child {
      border-left-color: $border-color-base;
    }
  }

  &-disabled &-checked {
    color: $radio-disabled-button-checked-color;
    background-color: $radio-disabled-button-checked-bg;
    border-color: $border-color-base;
    box-shadow: none;
  }
}

@keyframes antRadioEffect {
  0% {
    transform: scale(1);
    opacity: 0.5;
  }
  100% {
    transform: scale(1.6);
    opacity: 0;
  }
}

// Firefox hack
@supports (-moz-appearance: meterbar) and (background-blend-mode: difference, normal) {
  .#{$radio-prefix-cls} {
    vertical-align: text-bottom;
  }
}

```

* 新建 styles/variables/_radio.scss文件
```
@import "./common";
// $font-size-base
$radio-wrapper-margin-right: 8px;
```
* 新建 src/styles/index.scss文件
```
// radio样式
@import "../components/Radio/style";
```
* 接下来[[Typescript] 为第三方库添加声明文件 .d.ts](https://www.jianshu.com/p/1e262b487f26)
* 新建src/typings/index.d.ts文件
```
/// <reference path="custom-typings.d.ts" />
```
* 新建src/typings/custom-typings.d.ts文件
```
declare module 'rc-checkbox';

```
* 修改App.tsx文件
```
// 增加
import Radio from "./components/Radio/radio";
<Radio name="111">Radio</Radio>
<Radio name="111" disabled={true}>Radio2</Radio>
```
* 修改index.tsx文件
```
export { default as Radio } from "./components/Radio";
```
## 三、 运行项目
 执行命令
```
$ npm start
```
访问项目 可以看到Radio组件成功了！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-7fc5a5e3af91ee81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 四、 单元测试
新建src/Radio/radio.test.tsx文件·
```
import React from 'react';
import { mount, render } from 'enzyme';
import Radio, { Group, Button } from '..';
import focusTest from '../../../tests/shared/focusTest';
import mountTest from '../../../tests/shared/mountTest';
import rtlTest from '../../../tests/shared/rtlTest';

describe('Radio', () => {
  focusTest(Radio, { refFocus: true });
  mountTest(Radio);
  mountTest(Group);
  mountTest(Button);

  rtlTest(Radio);
  rtlTest(Group);
  rtlTest(Button);

  it('should render correctly', () => {
    const wrapper = render(<Radio className="customized">Test</Radio>);
    expect(wrapper).toMatchSnapshot();
  });

  it('responses hover events', () => {
    const onMouseEnter = jest.fn();
    const onMouseLeave = jest.fn();

    const wrapper = mount(<Radio onMouseEnter={onMouseEnter} onMouseLeave={onMouseLeave} />);

    wrapper.find('label').simulate('mouseenter');
    expect(onMouseEnter).toHaveBeenCalled();

    wrapper.find('label').simulate('mouseleave');
    expect(onMouseLeave).toHaveBeenCalled();
  });
});

```
执行命令
```
$ npm test
```
可以看到单元测试成功通过！
![image.png](https://upload-images.jianshu.io/upload_images/11846892-44fe42fa111d9409.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 五、编写storybook文档
* 新建`src/Radio/radio.stories.tsx`文件
```
import React from 'react';
import { storiesOf } from '@storybook/react';
import { action } from '@storybook/addon-actions';

import Radio from './radio'

const defaultRadio = () => (
    <div>
        <Radio onClick={action('default Radio')}>default radio</Radio>
    </div>
)

const radioWithDisabled = () => (
    <div>
        <Radio onClick={action('disabled radio')}   disabled={true}>disabled radio</Radio>
        <Radio onClick={action('unDisabled radio')}  >unDisabled radio</Radio>
    </div>
)


storiesOf('Radio 按钮', module)
    .addParameters({
        info: {
            text: `
        ## 引用方法
        ~~~js
        import {Radio} from 'echo-rui
        ~~~
        `
        }
    })
    .add('默认 Radio', defaultRadio)
    .add('禁用的 Radio',radioWithDisabled)
```
* 执行命令
```
$ npm run storybook
```
浏览器打开http://localhost:9009/,可以看到组件库文档生成了。
![image.png](https://upload-images.jianshu.io/upload_images/11846892-88eb962e94583778.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 六、 发布到npm
 ✅ ✅ ✅ ✅ ✅

 大功告成✌️✌️✌️
 ----
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️



