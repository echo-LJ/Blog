---
title: vueCli3-proxy
tags: proxy
---

Author: Echo
Time: 2019-07-24 15:28:39


##本地代理
在根目录下新建一个vue.config.js文件


```
const proxy_url = 'http://baidu.com';
const renewal_url = 'http://renewal-baidu.com';

module.exports = {
  productionSourceMap: false, //防止源码泄露
  devServer: {
    proxy: {
      '/ccs-api': {
        target: proxy_url, // 接口域名
        changeOrigin: true, //是否跨域
        pathRewrite: {
          '^/ccs-api': '' //需要rewrite的,
        }
      },
      '/renewal': {
        target: renewal_url, // 接口域名
        changeOrigin: true, //是否跨域
        pathRewrite: {
          '^/renewal': renewal_url //需要rewrite的,
        }
      }
    }
  }
  
}
```

Forward Proxy; Reverse Proxy 