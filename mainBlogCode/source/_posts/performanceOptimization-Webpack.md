---
title: 前端性能优化-webpack
date: 2021-06-28 16:10:08
tags: 性能优化
---

<meta name="referrer" content="no-referrer"/>
## 一、vueCli 查看打包后文件的大小占比
---
⚠️vue-cli2 使用 `webpack-bundle-analyzer`
```
// 用vue-cli2 构建的项目 中里已经集成了 
使用npm run build --report 命令即可
```
⚠️下面适用于：vue-cli3

**`1.1 安装依赖`**  
```
$ npm install webpack-bundle-analyzer --save-dev
```
**`1.2 配置vue.config.js`** 




```
chainWebpack: config => {
    // 查看打包文件体积大小
    config
      .plugin('webpack-bundle-analyzer')
      .use(require('webpack-bundle-analyzer').BundleAnalyzerPlugin,[
          {
            analyzerMode: 'server'   
          }
      ])
  }
  /**
  analyzerMode?: 'server' | 'static' | 'json' | 'disabled';
         * Can be "server", "static" or "disabled".
         * Defaults to "server".
         * In "server" mode analyzer will start HTTP server to show bundle report.
         * In "static" mode single HTML file with bundle report will be generated.
         * In "json" mode single JSON file with bundle report will be generated
         * In "disabled" mode you can use this plugin to just generate Webpack Stats JSON file by setting "generateStatsFile" to true.
         */
```
**`1.3 配置打包脚本`** 
在`package.json`的`scripts`中配置
```
$ "build": "vue-cli-service build --report"
```
执行命令：
```
$ npm run build
```
打开浏览器：[http://127.0.0.1:8888](http://127.0.0.1:8888) 之后 就会看到一个【可视化】的文件占比


![20200428150718890.png](https://upload-images.jianshu.io/upload_images/11846892-6dbebda468f26fd8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


❗️扩展：终端如果报出警告: (资产大小限制244KIB,可能回影响网络性能)。
![[图片上传中...(20200428150718890.png-e3528-1624502443756-0)]
](https://upload-images.jianshu.io/upload_images/11846892-a979dcca61ef8edd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

🧠解决办法：在`vue.config.js`中配置
```
module.exports = {
    //webpack配置
    configureWebpack: {
        //关闭 webpack 的性能提示
        performance: {
            hints:false
        }
        // 或者
        //警告 webpack 的性能提示
        performance: {
            hints:'warning',
            //入口起点的最大体积
            maxEntrypointSize: 50000000,
            //生成文件的最大体积
            maxAssetSize: 30000000,
            //只给出 js 文件的性能提示
            assetFilter: function(assetFilename) {
                return assetFilename.endsWith('.js');
            }
        }
    },
    // vue.config.js
    //     configureWebpack: config => {
    //         config.performance = {
    //            hints: 'warning',
    //           maxEntrypointSize: 50000000,
    //           maxAssetSize: 30000000,
    //           assetFilter: function(assetFilename) {
    //              return assetFilename.endsWith('.js');
    //          }
    //      }
    //    }
}
```
更多细节可参考：[webpack中文文档-性能(performance)](https://www.webpackjs.com/configuration/performance/)

## 二、移除console
---

如果你使用的是 webpack v5 或以上版本，你不需要安装这个插件。
webpack v5 自带最新的 `terser-webpack-plugin`。
如果使用 webpack v4，则必须安装 `terser-webpack-plugin` v4 的版本。


**`2.1 安装依赖`**  
```
$ npm install terser-webpack-plugin -D
```
**`2.2 配置vue.config.js`** 
```
const TerserPlugin = require('terser-webpack-plugin')

module.exports = {
    chainWebpack: config => {
        if (process.env.NODE_ENV === 'production') {
          config.optimization.minimizer([
            new TerserPlugin({
              test: /\.js(\?.*)?$/i,
              terserOptions: {
                compress: {
                  drop_console: true,
                  pure_funcs: ['console.log']
                }
              }
            })
          ])
        } else {
          // disable optimization during tests to speed things up
          config.optimization.minimize(false)
        }
      }
}
```
更多细节可参考：[webpack中文文档-TerserWebpackPlugin](https://webpack.docschina.org/plugins/terser-webpack-plugin/)

❓❓❓如果报错：
```
Error: optimization.minimizer() no longer supports being passed an array. Either switch to the new syntax (https://github.com/neutrinojs/webpack-chain#config-optimization-minimizers-adding) or downgrade to webpack-chain 4. If using Vue this likely means a Vue plugin has not yet been updated to support Vue CLI 4+.
```
🧠可重新配置
```
if (process.env.NODE_ENV === 'production') {
            config.optimization.minimizer('js')
                .use(require.resolve('terser-webpack-plugin'), [{ terserOptions: {
                    // 打包删掉注释
                    comments: true,
                    compress: {
                        drop_console: true,
                        drop_debugger: true
                        // pure_funcs: ["console.log"]

                    }
                } }])
        } else {
            // disable optimization during tests to speed things up
            config.optimization.minimize(false)
        }

```

❗️扩展：为什么删除生产环境的console?


>console.log：向web开发控制台打印一条消息，常用来在开发时调试分析。有时在开发时，需要打印一些对象信息，但发布时却忘记去掉console.log语句，这可能造成内存泄露。

在传递给console.log的对象是不能被垃圾回收 ♻️，因为在代码运行之后需要在开发工具能查看对象信息。所以最好不要在生产环境中console.log任何对象。
![下载 (3).png](https://upload-images.jianshu.io/upload_images/11846892-954576f740dc9d01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实例代码：
```
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Leaker</title>
</head>

<body>
  <input type="button" value="click">
  <script>
    !function () {
      function Leaker() {
        this.init();
      };
      Leaker.prototype = {
        init: function () {
          this.name = '*'.repeat(1e5);
          console.log("Leaking an object %o: %o", (new Date()), this); // this对象不能被回收
        }
      };
      document.querySelector('input').addEventListener('click', function () {
        new Leaker();
      }, false);
    }()
  </script>
</body>

</html>

```



这里结合Chrome的`Devtools–>Performance`做一些分析，操作步骤如下：

1. 开启Performance的记录
2. 执行CG按钮，创建基准参考线
3. 多次点击【click】按钮，新建Leaker对象
4. 执行CG按钮
5. 停止记录

![1069473253-5a266ef78ede4_fix732 (1).png](https://upload-images.jianshu.io/upload_images/11846892-8902e2e410b52f0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看出`【JS Heap】`线最后没有降回到基准参考线的位置，显然存在没有被回收的内存。如果将代码修改为
```
// console.log("Leaking an object %o: %o", (new Date()), this);
```
![1879498799-5a266f18551bf_fix732.png](https://upload-images.jianshu.io/upload_images/11846892-feb9af46d8bde50f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
重复上述的操作步骤，分析结果如下：

从对比分析结果可知，console.log打印的对象是不会被垃圾回收器回收的。
因此最好不要在页面中console.log任何对象，包括warn、error等兄弟，这样可能会影响页面的整体性能，特别在生产环境中，这些细节需要特别的关注。
## 三、压缩图片
---
**`3.1 安装依赖`** 
```
$ npm install terser-webpack-plugin -D
```
**`3.2 配置vue.config.js`** 
```
config.module
            .rule('images')
            .test(/\.(png|jpe?g|gif|svg)(\?.*)?$/)
            .use('image-webpack-loader')
            .loader('image-webpack-loader')
            .options({
                bypassOnDebug: true,
                disable: process.env.NODE_ENV !== 'production'
            });
```
## 四、UI库按需加载
---

对于大多数系统而言，都会使用一些一些UI组件库，例如Ant Design或者是Element UI，这些组件都是支持按需引入，我们在使用这些组件时，如果只用到了其中一部分组件，可以配置按需加载，在`main.js`中修改代码：
```
import {
    Pagination,
    Icon,
    Tabs,
} from 'ant-design-vue'
// import 'ant-design-vue/dist/antd.css'  已经通过babel引入 这里就不全局引入了

Vue.use(Pagination)
    .use(Icon)
    .use(Tabs)
```
然后修改babel.config.js，如下：
```
 "plugins": [
    ["import", { "libraryName": "ant-design-vue", "libraryDirectory": "es", "style": "css" }], // `style: true` 会加载 less 文件

  ]
```
这样，组件对应的js和css文件就可以实现按需加载.

## 五、路由懒加载
---
对于一般比较大型的B端管理系统项目，基本上都会使用Vue Router来管理路由，这些项目涉及的页面都比较多，所以为了防止首屏资源过大，需要采用路由懒加载资源即Code Splitting，将每个页面的资源进行分离，这个只需在router.js里配置即可：
```
// 采用箭头函数和import进行懒加载处理
$ component: () => import('./index.vue')
```

## 六、moment优化
---
**`6.1 问题描述`** 

根据打包分析图来看，主要是locale下moment的其他语言包占用体积较大。默认是en的语言包，所以在无需其他语言的情况下，可以直接忽略掉locale下的文件不打包。

忽略之前：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-1b86336fc64818ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

忽略之后：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-e42f5f204753e9d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`6.2 解决方案`** 
用webpack自带的IgnorePlugin插件
```
// vue.config.js
var webpack = require('webpack')

module.exports = {
 // ...此处省略其他配置
    
 chainWebpack: config => {
     config.plugin('ignore')
      .use(new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)); //忽略/moment/locale下的所有文件
  }
  
  // ...此处省略其他配置
}
```
**`6.3 解决方案-原理`** 
在webpack编译阶段, 如果引入的文件路径匹配`/^\.\/locale$/`，则会忽略这个文件， 也就不会被打包进去。

* 搜索`moment`包编译后的文件并未找到完全匹配`/^\.\/locale$/`这个正则的引入语句，只有`aliasedRequire('./locale/' + name)`这条语句和`locale`相关, 却又和正则匹配不上， 倒是在moment的src源文件中有`import ... from './locale'`。 但是在moment的`package.json`中main是指向编译后的文件并不是src文件，这就奇了怪了, 于是`debug IgnorePlugin`看了一下。

![image.png](https://upload-images.jianshu.io/upload_images/11846892-eb622a383efd5c8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 图中request真是`./locale`， 眼瞎了还是webpack的问题？按照dependencies的位置1853行查看moment编译后的文件， 定位到了确实是 `aliasedRequire('./locale/' + name)`， 怎么回事？

![image.png](https://upload-images.jianshu.io/upload_images/11846892-0c51e9c5d3e41581.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 原来webpack在编译时，遇到`require('./locale/' + name)`此类表达式时，webpack 会查找目录 `'./locale/'` 下符合正则表达式     `/^.*\.$/`的文件。由于 name 在编译时还是未知的，webpack 会将每个文件都作为模块引入到 bundle 中， 这就是为什么引入moment之后， 编译完的文件为什么会那么大的原因。

**`6.4 添加IgnorePlugin后, 需要设置locale怎么办?`** 
1. 在添加`webpack.IgnorePlugin`之后， 文件大小是减小了， 但是在设置`moment.locale('zh-cn')`之后， format之后的日期仍然是英文的，语言没有切换过来。

添加之前：打包之后包含momen.js的文件大小
![image.png](https://upload-images.jianshu.io/upload_images/11846892-227cf9891d45710f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
添加之后：打包之后包含momen.js的文件大小
![image.png](https://upload-images.jianshu.io/upload_images/11846892-f2474347815fbabe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 功能缺失肯定是不能接受的， 怎么办？怎么办？
3. 在moment文档上也提供了解决方案: `moment-locales-webpack-plugin`
```
$ npm install --save-dev moment-locales-webpack-plugin
```
```
// vue.config.js
new MomentLocalesPlugin({
  localesToKeep: ['zh-cn'],
})

 // const MomentLocalesPlugin = require('moment-locales-webpack-plugin')
        // config.plugin('moment-locales-webpack-plugin').use(
        //     new MomentLocalesPlugin({
        //         localesToKeep: ['zh-cn']
        //     })
        // );
```
4. moment默认locale是en，它必然会被打包进去, 如果需要配置其他语言，可以通过`localesToKeep`来配置， 其他没用到的语言包也就不会被打包进去了。

![下载 (3).png](https://upload-images.jianshu.io/upload_images/11846892-954576f740dc9d01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`vue.config.js配置如下代码`
```
config.plugin('moment-locales-webpack-plugin').use(
            new MomentLocalesPlugin({
                localesToKeep: ['es-us', 'ru', 'cs', 'hi', 'uk']
            })
        );
```
可以看到打包的时候都被打包删除掉了!
![[图片上传中...(下载 (3).png-2486c4-1624615311186-0)]
](https://upload-images.jianshu.io/upload_images/11846892-f582e7cb6b75a6c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`6.5 moment-locales-webpack-plugin原理分析`** 
1. 如果没有配置`option`， 用`IgnorePlugin`忽略所有语言包(en除外)
2. 如果设置`option`， 用 `ContextReplacementPlugin`插件设置`webpack`在编译阶段的查找规则，即查找指定的`locale`。
```
...
if (localesToKeep.length > 0) {
    var regExpPatterns = localesToKeep.map(function(localeName) {
        return localeName + '(\\.js)?';
    });
    return new ContextReplacementPlugin(
        /moment[\/\\]locale/,
        new RegExp('(' + regExpPatterns.join('|') + ')$') // 配置webpack编译阶段的查找规则， 即指定语言包
    );
} else {
    return new IgnorePlugin(/^\.\/locale$/, /moment$/);
}
...

```
## 七、webpack重复打包同名依赖包
---
最近安装了`webpack-bundle-analyzer`插件来分析打包构成，发现有一些包被重复的打包了多次，这样会让构建出来的包格外的臃肿。这主要是因为我们往往引用了很多的第三方包，而很多工具类的库也会被别的包间接的依赖，所以就导致了重复打包的现象，例如下图的bn.js。
![截屏2021-06-26 上午10.55.07.png](https://upload-images.jianshu.io/upload_images/11846892-63f9f10d9cf5e6d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`7.1 解决方案`** 
在`webpack`的`resolve`下面添加如下配置：
```
// 第一种方法
module.exports = {
    resolve: {
        alias:{
            'bn.js': path.resolve(process.cwd(), 'node_modules', 'bn.js')
        }
    }
};
// 第二种方法
module.exports = {
    configureWebpack:{
        resolve:{
            alias:{
                'bn.js': path.resolve(process.cwd(), 'node_modules', 'bn.js')
            }
        }
    }
};

// 第三种方法
const path = require('path');//引入path模块
function resolve(dir){
    return path.join(__dirname,dir)//path.join(__dirname)设置绝对路径
}
module.exports={
    chainWebpack: config =>{
        config.resolve.alias
        .set("@", resolve("src"))　
    }
}
```

`resolve.alias`的作用其实就是添加包的别名并强行指定引用统一路径，配置完的效果如下，只能看到一个bn.js了。

`优化之后`
![image.png](https://upload-images.jianshu.io/upload_images/11846892-c4fe95c3552a5aeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-54f5aa2861442249.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`优化之前`
![image.png](https://upload-images.jianshu.io/upload_images/11846892-75fba7826c8c6a6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](https://upload-images.jianshu.io/upload_images/11846892-0fa9db3e53298e6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 八、有选择的使用prefetch和preload
---
**`prefetch`** 
```
<link rel="prefetch" ></link>
```
这段代码告诉浏览器，这段资源将会在未来某个导航或者功能要用到，但是本资源的下载顺序权重比较低。也就是说prefetch通常用于加速下一次导航，而不是本次的。
**`preload`** 
```
<link rel="preload" ></link>
```
preload通常用于本页面要用到的关键资源，包括关键js、字体、css文件。`preload`将会把资源得下载顺序权重提高，使得关键数据提前下载好，优化页面打开速度。

在使用`Vue Cli`生成的项目里，当我们配置了路由懒加载后，默认情况下`webpack`在构建时会对所有的懒加载资源进行`prefetch`和`preload`，所以当你打开首页时，会看到大量的`prefetch`和`preload`请求，如下图：

![v2-c0eac86b7041fe0a6a04d8a4ea9c01d0_1440w.jpeg](https://upload-images.jianshu.io/upload_images/11846892-d5797aa64fc21702.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
// 禁止prefetch和preload
chainWebpack: (config) => {
  config.plugins.delete('prefetch')
  config.plugins.delete('preload')
}
// 有选择的prefetch和preload
config.plugin('prefetch').tap(options => {
    options[0].fileBlacklist = options[0].fileBlacklist || []
    options[0].fileBlacklist.push(/myasyncRoute(.)+?\.js$/)
    return options
})
```
上面代码修改`vue.config.js`的`chainWebpack`来添加配置。

---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️


参考链接：
* [解读生产环境为何避免使用console](https://segmentfault.com/a/1190000012295395)
* [Vue Cli项目B端系统常见性能优化技巧](https://zhuanlan.zhihu.com/p/362547907)
* [vue打包优化——vendor体积过大](https://www.jianshu.com/p/4f8f36944a46)
* [关于moment打包的那些事](https://juejin.cn/post/6844903987632685063)
* [webpack重复打包同名依赖包](https://blog.csdn.net/u010352770/article/details/101538528)
