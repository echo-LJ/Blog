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

## Yarn 的安装理念
----
它的出现(npm v3时期，还没有package-lock.json)是为了解决历史上 npm 的某些不足（比如 npm 对于依赖的完整性和一致性保障，以及 npm 安装速度过慢的问题等）
* 确定性：通过 yarn.lock 等机制，保证了确定性。即不管安装顺序如何，相同的依赖关系在任何机器和环境下，都可以以相同的方式被安装。
* 采用模块扁平安装模式：将依赖包的不同版本，按照一定策略，归结为单个版本，以避免创建多个副本造成冗余（npm 目前也有相同的优化）。
* 采用缓存机制，实现了离线模式

> 相比 npm，Yarn 另外一个显著区别是 yarn.lock 中子依赖的版本号不是固定版本。这就说明单独一个 yarn.lock 确定不了 node_modules 目录结构，还需要和 package.json 文件进行配合。

甚至还有一个专门的 synp 工具，它可以将 yarn.lock 转换为 package-lock.json

#### Yarn 缓存

查看缓存内容
```
yarn cache dir
```

Yarn 默认使用 `prefer-online` 模式，即优先使用网络数据。如果网络数据请求失败，再去请求缓存数据

#### Yarn 安装机制
* 检测（checking）: 
  检测项目中是否存在一些 npm 相关文件，比如 package-lock.json 等。如果有，会提示用户注意：这些文件的存在可能会导致冲突。在这一步骤中，也会检查系统 OS、CPU 等信息。
* 解析包（Resolving Packages）
  解析package.json 定义的 dependencies、devDependencies、optionalDependencies 的内容，这属于首层依赖。
  接着采用遍历首层依赖的方式获取依赖包的版本信息 
* 获取包（Fetching Packages）
  检查缓存中是否存在当前的依赖包，同时将缓存中不存在的依赖包下载到缓存目录。
* *链接包（Linking Packages）
* 构建包（Building Packages


## 破解依赖管理困境
----
> 如何理解“嵌套地狱”呢？
项目依赖树的层级非常深，不利于调试和排查问题；
依赖树的不同分支里，可能存在同样版本的相同依赖。比如直接依赖 A 和 B，但 A 和 B 都依赖相同版本的模块 C，那么 C 会重复出现在 A 和 B 依赖的 node_modules 中。

* npm 包的安装顺序对于依赖树的影响很大。模块安装顺序可能影响 node_modules 内的文件数量。

解决方案：优雅的方式是使用 `npm dedupe` 命令

## CI 环境上的 npm 优化
----
#### 合理使用 npm ci 和 npm install
npm ci 就是专门为 CI 环境准备的安装命令，相比 npm install 它的不同之处在于：
* npm ci 要求项目中必须存在 package-lock.json 或 npm-shrinkwrap.json；
* npm ci 完全根据 package-lock.json 安装依赖，这可以保证整个开发团队都使用版本完全一致的依赖； 
* 正因为 npm ci 完全根据 package-lock.json 安装依赖，在安装过程中，它不需要计算求解依赖满足问题、构造依赖树，因此安装过程会更加迅速；
**package-lock.json 中已经缓存了每个包的具体版本和下载链接，你不需要再去远程仓库进行查询，即可直接进入文件完整性校验环节，减少了大量网络请求。**
* npm ci 在执行安装时，会先删除项目中现有的 node_modules，然后全新安装；
* npm ci 只能一次安装整个项目所有依赖包，无法安装单个依赖包；
* 如果 package-lock.json 和 package.json 冲突，那么 npm ci 会直接报错，并非更新 lockfiles；
* npm ci 永远不会改变 package.json 和 package-lock.json。

**我们在 CI 环境使用 npm ci 代替 npm install，一般会获得更加稳定、一致和迅速的安装体验。**

#### 为什么要 lockfiles，要不要提交 lockfiles 到仓库？

**package-lock.json 文件的作用是锁定依赖安装结构，目的是保证在任意机器上执行 npm install 都会得到完全相同的 node_modules 安装结果。**

>为什么单一的 package.json 不能确定唯一的依赖树：

* 不同版本的 npm 的安装依赖策略和算法不同；

* npm install 将根据 package.json 中的 semver-range version 更新依赖，某些依赖项自上次安装以来，可能已发布了新版本。

package-lock.json中并不是所有的子依赖都有 `dependencies` 属性，只有子依赖的依赖和当前已安装在根目录的 node_modules 中的依赖冲突之后，才会有这个属性
#### 要不要提交 lockfiles 到仓库？
* 如果开发一个应用，我建议把 package-lock.json 文件提交到代码版本仓库。这样可以保证项目组成员、运维部署成员或者 CI 系统，在执行 npm install 后，能得到完全一致的依赖安装内容。
* 如果你的目标是开发一个给外部使用的库，那就要谨慎考虑了，因为库项目一般是被其他项目依赖的，在不使用 package-lock.json 的情况下，就可以复用主项目已经加载过的包，减少依赖重复和体积。
**`一个推荐的做法是：`**把 package-lock.json 一起提交到代码库中，不需要 ignore。但是执行 npm publish 命令，发布一个库的时候，它应该被忽略而不是直接发布出去。

#### 为什么有 xxxDependencies？
npm 设计了以下几种依赖类型声明：

* dependencies 项目依赖

* devDependencies 开发依赖: 不会被自动下载,比如 Webpack，预处理器 babel-loader、scss-loader，测试工具 E2E、Chai 等，这些都是辅助开发的工具包，无须在生产环境使用

* peerDependencies 同版本依赖:如果你安装我，那么你最好也安装我对应的依赖。
>peerDependencies 主要的使用场景:
插件不能单独运行
插件正确运行的前提是核心依赖库必须先下载安装
我们不希望核心依赖库被重复下载
插件 API 的设计必须要符合核心依赖库的插件编写规范
在项目中，同一插件体系下，核心依赖库版本最好相同

* bundledDependencies 捆绑依赖: bundledDependencies 和 npm pack 打包命令有关.
在 bundledDependencies 中指定的依赖包，必须先在 dependencies 和 devDependencies 声明过，否则在 npm pack 阶段会进行报错。

* optionalDependencies 可选依赖: 不建议大家使用，因为它大概率会增加项目的不确定性和复杂性.
#### 团队最佳实操建议
* 优先使用 npm v5.4.2 以上的 npm 版本，以保证 npm 的最基本先进性和稳定性。

* 项目的第一次搭建使用 npm install 安装依赖包，并提交 package.json、package-lock.json，而不提交 node_modules 目录。

* 其他项目成员首次 checkout/clone 项目代码后，执行一次 npm install 安装依赖包。

对于升级依赖包的需求：

依靠 npm update 命令升级到新的小版本；

依靠 npm install @ 升级大版本；

也可以手动修改 package.json 中版本号，并执行 npm install 来升级版本；

* 本地验证升级后新版本无问题，提交新的 package.json、package-lock.json 文件。

* 对于降级依赖包的需求：执行 npm install @ 命令，验证没问题后，提交新的 package.json、package-lock.json 文件。

删除某些依赖：

* 执行 npm uninstall 命令，验证没问题后，提交新的 package.json、package-lock.json 文件；

* 或者手动操作 package.json，删除依赖，执行 npm install 命令，验证没问题后，提交新的 package.json、package-lock.json 文件。

* 任何团队成员提交 package.json、package-lock.json 更新后，其他成员应该拉取代码后，执行 npm install 更新依赖。

* 任何时候都不要修改 package-lock.json。

* 如果 package-lock.json 出现冲突或问题，建议将本地的 package-lock.json 文件删除，引入远程的 package-lock.json 文件和 package.json，再执行 npm install 命令。






----
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️

参考链接：https://kaiwu.lagou.com/course/courseInfo.htm?courseId=584#/detail/pc?id=5907





