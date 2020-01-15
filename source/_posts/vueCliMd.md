---
title: vue-cli3项目展示本地Markdown文件的方法
date: 2020-01-15 15:05:46
tags:
---

【版本】
```
vue-cli3
webpack@4.33.0
```
【步骤】

1. 安装插件
```
$ npm install markdown-it --save
```
2. 新建markdownLoader.js文件(自定义loader)
```
const markdown = require('markdown-it');

function markLoader(src) {
  const md = markdown({
    html: true,
    typographer: true,
  });
  const html = md.render(src);

  return (
    '<template>\n'
    + `<div class="markdown">${html}</div>\n`
    + '</template>\n'
  );
}
module.exports = markLoader;
```
3. 在vue.config.js(如果没有,在根目录下自行创建)文件中配置
```
module.exports = {
  configureWebpack: config => {
    config.module.rules.push({
      // 处理markdown文件
      test: /\.md$/,
      use: [
        {
          loader: "vue-loader"
        },
        {
          loader: require.resolve("./src/common/markdownLoader")
        }
      ],
    },
    );
  }
};
```

4.保存后重新启动项目(尤其是修改自定义lodaer和vue.config.js配置文件,必须重新启动项目生效)