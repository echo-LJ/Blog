---
title: 乾坤-微前端
date: 2023-09-04 15:16:16
tags: qiankun
---

<meta name="referrer" content="no-referrer"/>

## 微前端

一个应用，当不断迭代的时候，`功能会越来越多`，`代码量随着也会变得越来越大`。进而代码之间的`耦合度灰越来越高`，进而代码之间的耦合性会变高，这样导致`开发和维护很糟心`，动一发而牵全身。于是有了 **`微前端`** 来解这个问题.

按照可以将这个应用拆分成多个项目，每个项目都是独立的仓库，独立的部署，然后利用微前端再组合成在一起。

**微前端是一种由独立交付的多个前端应用组成整体的架构风格。**将前端应用分解成一些，更小、更简单的能够独立开发，测试，部署的小块，在用户看来仍然是内聚的一个产品

## qiankun-乾坤

乾坤是基于`single-spa`框架的, single-spa是一个将多个单页面应用聚合为一个整体应用的 JavaScript 微前端框架, 而乾坤在single-spa的基础上主要做了`资源的加载和应用之间的隔离`。乾坤为single-spa提供了更多的功能扩展，包括`资源隔离`、`样式隔离`、`动态加载`和`沙箱隔离`等，从而实现了在多个子应用之间共享组件和状态的能力。

#### single-spa的使用示例

通过`registerApplication`来注册子应用，然后当页面的路由与`activeWhen`首次相匹配时，就会触发app函数的执行。这个函数里面返回一些生命周期，在这些生命周期里做一些渲染。

```
import * as singleSpa from 'single-spa';

//注册应用
singleSpa.registerApplication({
    name: 'app1',
    //应用激活路由条件
    activeWhen: '/app1',
    app(){
      const domEl = document.getElementById('#container')

      return {
        //首次挂载时执行
        bootstrap(){},
        //挂载时执行
        mount(){
          domEl.innerHTML = 'App 1 is mounted!'
        }, 
        //卸载时执行
        unmount(){
          domEl.innerHTML = ''
        }
      }
    }
})
singleSpa.registerApplication({
    name: 'app2',
    activeWhen: '/app2',
    app(){
      const domEl = document.getElementById('#container')

      return {
        bootstrap(){},
        mount(){
          domEl.innerHTML = 'App 2 is mounted!'
        }, 
        unmount(){
          domEl.innerHTML = ''
        }
      }
    }
})

singleSpa.start();

```
#### 乾坤的使用示例

在single-spa中配置的是函数，而在乾坤中配置的是文件地址。
```
import {registerMicroApps, start} from 'qiankun';

registerMicroApps([
    {
      name: 'app1',
      entry: {
        scripts: ['/app1.js'],
        styles:['/app1.css']
      },
      container: '#container',
      activeRule: '/app1',
    },
    {
      name: 'app2',
      entry: {
        scripts: ['/app2.js']
      },
      container: '#container',
      activeRule: '/app2',
    },
  ]);
  start();
```
#### 资源配置
配置的资源由 [import-html-entry](https://www.npmjs.com/package/import-html-entry)中的`importEntry`方法来处理,调用后返回template和execScripts：

* template就是要插入到页面中的html内容，在加载微应用时会把它插入到配置的container节点中。
* 这里面对`样式`和`脚本`做了处理，对于有url的link样式，都会通过网络请求转为style的形式，这样的作用就是在插入到页面中时方便样式隔离处理.
* 对于所有的脚本内容，会直接从template中去掉。需要调用execScripts来执行脚本，执行时会在脚本内容中加上with(global)然后执行，这样会影响全局对象进而进行脚本隔离。

```
import {importEntry} from 'import-html-entry'

importEntry({
    html:'<div>content</div>',
    scripts:['http://localhost:5000/index.js'],
    styles:['http://localhost:5000/index.css'],
}).then(res => {
    console.log(res)
    res = {
      template: `<style>
        /* http://localhost:5000/index.css */
        body{
          color: #0ff;
        }
      </style>
      <div>content</div>
      <!--  script http://localhost:5000/index.js replaced by import-html-entry -->`
      execScripts: ƒ execScripts(proxy, strictGlobal)
      ...
    }**/
})
```

### 沙盒
* 主要作用： 用于隔离和保护子应用程序（微前端子应用）的运行环境。隔离微应用之间的`脚本`和`样式`影响， 处理style、link、script类型的标签。
* 处理的时机: 第一个是在首次加载的时候，第二个是在微应用运行中.

* 在运行中处理方案： qiankun重写了下面这些原声方法，这样就可以`监听到新添加的节点`,然后对style、link、script标签进行处理。

```
if (
    //原始方法未被替换
    HTMLHeadElement.prototype.appendChild === rawHeadAppendChild &&
    HTMLBodyElement.prototype.appendChild === rawBodyAppendChild &&
    HTMLHeadElement.prototype.insertBefore === rawHeadInsertBefore
) {
    //替换原始方法
    HTMLHeadElement.prototype.appendChild = getOverwrittenAppendChildOrInsertBefore({
      rawDOMAppendOrInsertBefore: rawHeadAppendChild,
      containerConfigGetter,
      isInvokedByMicroApp,
    }) as typeof rawHeadAppendChild;
    ...
}
```

### 脚本隔离
* 方式： 一种是快照拷贝的方式，一个是基于proxy的方式。乾坤会根据当前环境是否支持proxy来决定用那种方式。

#### 快照方式

在创建微应用的时候会实例化一个沙盒对象，它有两个方法，active是在激活微应用的时候执行，而inactive是在离开微应用的时候执行。

**整体思路**： 在激活微应用时将当前的window对象拷贝存起来，然后从modifyPropsMap中恢复这个微应用上次修改的属性到window中。
在离开微应用时会与原有的window对象做对比，将有修改的属性保存起来，以便再次进入这个微应用时进行数据恢复，然后把有修改的属性值恢复到以前的状态。

```
//遍历对象属性，用callbackFn回调
function iter(obj: typeof window, callbackFn: (prop: any) => void) {
  for (const prop in obj) {
    if (obj.hasOwnProperty(prop) {
      callbackFn(prop);
    }
  }
} 
class SnapshotSandbox implements SandBox {
  active() {
    // 记录当前快照
    this.windowSnapshot = {} as Window;
    iter(window, (prop) => {
      this.windowSnapshot[prop] = window[prop];
    });

    // 恢复之前的变更
    Object.keys(this.modifyPropsMap).forEach((p: any) => {
      window[p] = this.modifyPropsMap[p];
    });
  }

  inactive() {
    this.modifyPropsMap = {};
    iter(window, (prop) => {
      if (window[prop] !== this.windowSnapshot[prop]) {
        // 保存变更
        this.modifyPropsMap[prop] = window[prop];
        // 恢复到以前的状态
        window[prop] = this.windowSnapshot[prop];
      }
    });
  }
}
```

#### proxy方式

微应用中的script内容都会加with(global)来执行，这里global是全局对象，如果是proxy的隔离方式那么他就是下面新创建的proxy对象。我们知道with可以改变里面代码的作用域，也就是我们的微应用全局对象会变成下面的这个proxy。当设置属性的时候会设置到proxy对象里，在读取属性时先从proxy里找，没找到再从原始的window中找。也就是你在微应用里修改全局对象的属性时不会在window中修改，而是在proxy对象中修改。因为不会破坏window对象，这样就会隔离各个应用之间的数据影响。

```
class implements SandBox {
  constructor(name: string) {
    const rawWindow = window;

    const proxy = new Proxy(fakeWindow, {
      set: (target: FakeWindow, p: PropertyKey, value: any): boolean => {
        //修改对象直接保存到proxy对象中
        target[p] = value;
      },

      get(target: FakeWindow, p: PropertyKey): any {
        // 从proxy对象中获取，获取不到从fakeWindow中获取
        const value = p in target
          ? (target as any)[p]
          : (rawWindow as any)[p];

        return value;
      }
    })
  }
}
```

### 样式隔离

默认情况下沙箱可以确保单实例场景子应用之间的样式隔离（切换子应用时会卸载添加的样式标签），但是无法确保主应用跟子应用、或者多实例场景的子应用样式隔离，需要手动增加配置参数才能激活下面的隔离。

#### 域隔离
为每个css规则添加特定的前缀来起到隔离的作用，例如微应用中的样式是`p{color:#000}`，处理后为`.app1 p {color:#000} `

* 创建一个临时的style节点用来后续处理
* 调用process来处理style规则
* 通过style的sheet属性来获取一条条规则
* 然后调用ruleStyle进行转化，转化是通过正则进行匹配然后替换
* 最后把转化后的内容替换到原有的style节点中

```
class ScopedCSS {  
  constructor() {
    //创建一个临时style节点，用来处理样式
    this.swapNode = document.createElement('style');
    rawDocumentBodyAppend.call(document.body, styleNode);
  }

  process(styleNode: HTMLStyleElement, prefix: string = '') {
    //style节点内容不为空
    if (styleNode.textContent !== '') {
      //获取内部的样式规则
      this.swapNode.appendChild(styleNode.textContent);
      const sheet = this.swapNode.sheet as any;
      //获取替换后的css
      const css = this.ruleStyle(sheet, prefix);

      //应用替换后的css
      styleNode.textContent = css;
    }
  }
  private ruleStyle(rule: CSSStyleRule, prefix: string) {
    const rootSelectorRE = /((?:[^\w\-.#]|^)(body|html|:root))/gm;

    const selector = rule.selectorText.trim();

    let { cssText } = rule;

    //\s\S 匹配所有   这里可以匹配到如 body{
    cssText = cssText.replace(/^[\s\S]+{/, (selectors) =>
      // div,span{}  逗号拆分处理
      selectors.replace(/(^|,\n?)([^,]+)/g, (item, p, s) => {
        // handle div,body,span { ... }
        if (rootSelectorRE.test(item)) {
          return item.replace(rootSelectorRE, (m) => {
            const whitePrevChars = [',', '('];
            //处理前面有, (
            if (m && whitePrevChars.includes(m[0])) {
              return `${m[0]}${prefix}`;
            }

            //直接返回prefix
            return prefix;
          });
        }
        //p 匹配到逗号 ， s为 span{     后面这里会去掉空白
        return `${p}${prefix} ${s.replace(/^ */, '')}`;
      }),
    );
    return cssText;
  }
}
```

#### shadow dom

在当前微应用的根节点开启shadow，然后子节点的操作都是在shadowRoot上来进行隔离。

####
---

总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接:
* [深入解析微前端乾坤原理](https://zhuanlan.zhihu.com/p/414468874)
