---
title: 前端工程化---琐碎小记
date: 2020-12-17 10:00:00
tags: 前端工程化
---
<meta name="referrer" content="no-referrer"/>

## Node.js诞生带来的技术更新？
----
1. 改进： 
  通过Node.js,除了前端工具链、工程化得以发展，前端也实现BFF（Backend For Frontend）
  * 自行编写后端服务，实现数据的适配，应用场景包括接口的整合编排、字段裁剪；
  * 实现SSR（服务端渲染直出）技术，达到提升首屏性能以及 SEO 友好的目的；

2. 改进带来的成本：

  落地Node.js 和 SSR架构模式 从而就要关心服务器的运维、部署、发布、监控。

3. 成本带来的新技术变更：

  Serverless概念：运行和构建不需要服务器管理的应用程序的概念。
  * 将服务器的运维功能都交给 Serverless 平台进行管理，研发人员只需要专注于实现云函数即可完成功能开发。

## 前端技术架构的特点？
----
* 组件化是基本 UI 架构
* 依托于 SSR 同构技术以及心智负担的最小化，框架层面提供的虚拟 DOM 会成为生态标配；
* 数据状态管理方案将会以职责单一、minimal necessary 为目标，以组合性、函数式为理念，而不以双向数据流和单向数据流的区分为重点；

## CSR → SSR → NSR → ESR 几种渲染方案的特点？
----
**CSR：Client Side Rendering**：
* 实现了前后端架构分离，职责分离。
* TTFB(网站加载) 时间最小，但由于客户端和服务端会有多次交互（获取静态资源、获取数据）才能进行渲染，实际首屏效果以及 FCP/FMP 时间不够理想。

**SSR：Server Side Rendering**：
* 在服务端完成页面模板、数据预取、填充，并且在服务端就可以将完整的 HTML 内容返回给浏览器。

使性能优化到极致 还可以发展： `Streaming server rendering（流式 SSR 渲染）`或 `Progressive Rehydration（渐进式 SSR 渲染）`

* 流式 SSR 渲染，允许服务端通过 stream 的方式向浏览器发送 HTML 内容。在 React 中，我们可以使用renderToNodeStream()方法来完成流式 SSR 渲染。

* 渐进式 SSR 渲染可以允许在 hydrating 没有完全结束前，部分已经渲染并注水完成的页面内容，可以优先完成交互响应。React 专门将Partial Hydration开了一个 PR 来讨论。

**NSR：Native Side Rendering**：
简单说就是通过 Native 渲染生成 HTML 数据，并且缓存在客户端。这样一来，对于一个 hybrid WebView 的用户访问，会优先从离线包中加载离线页面模板，再通过前端 Ajax/或客户端能力请求数据，最终完成页面完整的展示。

* 好处：我们将服务器的渲染工作放在了一个个独立的移动设备中，并借助离线存储技术，实现了页面的预加载，同时又不会增加额外的服务器压力。

**ESR：Edge Side Rendering**：

* 边缘计算，是指在靠近物或数据源头的一侧，采用网络、计算、存储、应用核心能力为一体的开放平台，就近提供最近端服务。

## npm安装机制
----
#### npm在工程项目中的作用？
* 负责依赖的安装和维护
* 通过npm scripts 串联起项目的智能部分，让独立的环节自动运转起来。
#### npm的安装机制
`扩展:` Ruby的Gem、Python的pip都是全局安装
* 优先安装依赖包到当前项目目录，使得不同应用项目的依赖各成体系，同时还减轻了包作者的 API 兼容性压力。
* 安装机制缺点： 因为多个项目需要安装相同的依赖，会导致同一个依赖包在电脑中多次安装.

**`npm install的机制示意图`**

![image.png](https://s0.lgstatic.com/i/image2/M01/02/A9/Cip5yF_axkqAclTFAAJmlxGYSmI551.png)

#### npm的缓存机制
**对于一个依赖包的同一版本进行本地化缓存，是当代依赖包管理工具的一个常见设计。**
执行命令
```
npm config get cache
```

得到配置缓存的根目录在 `/Users/cehou/.npm`（ Mac OS 中，npm 默认的缓存位置） 当中。我们 cd 进入 `/Users/cehou/.npm` 中可以发现`_cacache`文件。事实上，在 npm v5 版本之后，缓存数据均放在根目录中的_cacache文件夹中。
使用以下命令清除 /Users/cehou/.npm/_cacache 中的文件：
```
npm cache clean --force
```
接下来打开`_cacache`文件，看看 npm 缓存了哪些东西，一共有 3 个目录：
* content-v2: 一些二进制文件，将二进制文件的扩展名改为.tgz，解压之后就是npm包资源

* index-v5：同上操作，得到的是content-v2里文件的索引

* tmp
>这些缓存如何被储存并被利用的呢？

！！在npm v5版本才支持缓存

npm install => 通过`pacote`把相应的包解压在对应的 node_modules 下面。=> npm下载依赖时，先下载到缓存当中，在解压到项目的node_modules中。=> pacote依赖npm-registry-fetch来下载包，npm-registry-fetch可以通过设置cache属性，在给定的路径下根据
IETF RFC 7234生成缓存数据。

在每次安装资源时，根据package-lock.json 中存储的 integrity、version、name 信息生成一个唯一的 key，这个 key 能够对应到 index-v5 目录下的缓存记录。如果发现有缓存资源，就会找到 tar 包的 hash，根据 hash 再去找缓存的 tar 包，并再次通过pacote把对应的二进制文件解压到相应的项目 node_modules 下面，省去了网络下载资源的开销。
## npm的小技巧
----

#### 自定义npm init
`原理`： 调用shell脚本输出一个初始化的package.json
实现一个更加灵活的自定义功能：

使用 prompt() 方法，获取用户输入并动态产生的内容：
```
.npm-init.js
const desc = prompt('请输入项目描述', '项目描述...')
module.exports = {
  key: 'value',
  name: prompt('name?', process.cwd().split('/').pop()),
  version: prompt('version?', '0.0.1'),
  description: desc,
  main: 'index.js',
  repository: prompt('github repository url', '', function (url) {
    if (url) {
      run('touch README.md');
      run('git init');
      run('git add README.md');
      run('git commit -m "first commit"');
      run(`git remote add origin ${url}`);
      run('git push -u origin master');
    }
    return url;
  })
}
```
* 执行命令来确保 npm init 所对应的脚本指向正确的文件：
```
npm config set init-module ~\.npm-init.js
```
还可以通过配置npm init 默认字段来自定义npm init内容
#### 利用 `npm link`，高效率在本地调试以验证包的可用性

> 当我们开发完成一个组件库，如何验证该组件在我的业务项目中正常运行呢？

**举个🌰**：正在开发项目 project 1，其中有个包 package 1，对应 npm 模块包名称是 npm-package-1，我们在 package 1 项目中加入了新功能 feature A，现在要验证在 project 1 项目中能否正常使用 package 1 的 feature A，你应该怎么做？

* 在 package 1 目录中，执行 `npm link`，这样 npm link 通过链接目录和可执行文件，实现 npm 包命令的全局可执行。

* 在 project 1 中创建链接，执行 npm link npm-package-1 命令时，它就会去 /usr/local/lib/node_modules/ 这个路径下寻找是否有这个包，如果有就建立软链接。

* 就可以在 project 1 的 node_module 中会看到链接过来的模块包 npm-package-1，此时的 npm-package-1 就带有最新开发的 feature A，这样一来就可以在 project 1 中正常开发调试 npm-package-1。

* !当然别忘了，调试结束后可以执行 npm unlink 以取消关联。

**`npm link原理`**： 

1. 为目标 npm 模块（npm-package-1）创建软链接，将其链接到全局 node 模块安装路径 /usr/local/lib/node_modules/ 中；

2. 为目标 npm 模块（npm-package-1）的可执行 bin 文件创建软链接，将其链接到全局 node 命令安装路径 /usr/local/bin/ 中。

#### npx的作用

npx 由 npm v5.2 版本引入，解决了 npm 的一些使用快速开发、调试，以及项目内使用全局模块的痛点。

* 项目安装使用eslint
**`举个npm🌰`**: 
在传统的npm 模式下，使用eslint,需要先执行命令安装
```
npm install eslint --save-dev
```
然后在项目根目录下执行：

```
./node_modules/.bin/eslint --init
./node_modules/.bin/eslint yourfile.js
```
或者通过项目脚本和 package.json 的 npm scripts 字段调用 ESLint。
**`举个npx🌰`**: 
执行命令即可
```
npx eslint --init
npx eslint yourfile.js
```
> 为什么 npx 操作起来如此便捷呢？

这是因为它可以直接执行 node_modules/.bin 文件夹下的文件。在运行命令时，npx 可以自动去 node_modules/.bin 路径和环境变量 $PATH 里面检查命令是否存在，而不需要再在 package.json 中定义相关的 script。

**npx 另一个更实用的好处是：npx 执行模块时会优先安装依赖，但是在安装执行后便删除此依赖，这就避免了全局安装模块带来的问题。**

## npm 多源镜像
----
npm 中的源（registry），其实就是一个查询服务。
* 举个🌰： https://registry.npmjs.org/react，就会看到 react 模块所有版本的信息。
* 设置安装源： 
```
npm config set
```
* 使用多个安装源的项目，可以通过 npm-preinstall 的钩子，通过 npm 脚本，在安装公共依赖前自动进行源切换：
```
"scripts": {
    "preinstall": "node ./bin/preinstall.js"
}
```

```
//preinstall.js : 通过node.js执行npm config set命令。
require(' child_process').exec('npm config get registry', function(error, stdout, stderr) {
  if (!stdout.toString().match(/registry\.x\.com/)) {
    exec('npm config set @xscope:registry https://xxx.com/npm/')
  }
})
#### 私服扩展
* 部署私有 npm 镜像，可以提高npm下载第三方依赖包的速度缓慢的问题
* 确保高速、稳定的 npm 服务，使发布私有模块更加安全。
* 审核机制也可以保障私服上的 npm 模块质量和安全
** 推荐搭建npm私服的工具**
* nexus
* verdaccio
* cnpm
```






----
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️





