---
title: Axios封装
date: 2019-01-01 10:00:00
tags: Axios
---

<meta name="referrer" content="no-referrer"/>

[axios文档地址](https://www.npmjs.com/package/axios)

在vue项目中，和后台交互获取数据这块，我们通常使用的是`axios`库，它是基于`promise`的http库，可运行在浏览器端和node.js中。他有很多优秀的特性，例如`拦截请求和响应、取消请求、转换json、客户端防御cSRF`等。所以我们的尤大大也是果断放弃了对其官方库vue-resource的维护，直接推荐我们使用axios库.

`
本次封装以vue项目为例做实践。
`
## 安装
---

```
$ npm install axios
```

## 引入
---

在项目的src目录下新建文件夹request,文件夹下新建http.js和api.js文件：


 * http.js: 封装axios
 * api.js: 用来统一管理接口

```
// 在http.js中引入axios
import axios from 'axios'; // 引入axios
import QS from 'qs'; // 引入qs模块，用来序列化post类型的数据，后面会提到

```

### 环境的切换->配置不同的baseURL

我们的项目环境可能有开发环境、测试环境和生产环境.
我们通过node的环境变量来匹配我们的默认的接口url前缀
axios.defaults.baseURL可以设置axios的默认请求地址

```
const baseURL_Map = {
  dev：'https://www.baidu.com',
  uat: 'https://www.ceshi.com',
  product: 'https://www.production.com'
}
axios.defaults.baseURL = baseURL_Map[process.env.NODE_ENV] 
```
扩展：
### process.env.NODE_ENV
* process对象是全局变量: 提供当前node.js的有关信息，以及控制当前node.js的有关进程。因为是全局变量，它对于node应用程序是始终可用的，无需require()
* process.env属性返回一个包含用户环境信息的对象
* process.env.NODE_ENV:不是process.env对象上原有的属性，是我们自己添加进去的一个环境变量，用来确定当前所处的开发阶段。
* 测试环境： uat; 生产环境：product; 开发环境： dev

### 设置请求超时变量
```
// 例如超过了10s，就会告知用户当前请求超时，请刷新等。

const TIMEOUT = 30000;
```

扩展： 
* 统一处理所有接口的超时时间： `axios.defaults.timeout = 10000;`

### 设置post请求头
```
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8';
```

 此处无需设置，在下面封装的时候会针对get和post请求做统一对应处理

### 定义HTTP响应状态码的范围
```
/**
 * 判断 HTTP 请求是否成功
 * @param {number} status - HTTP 状态码
 * @returns {Boolean}
 */
const isBadRequest = status => status >= 300;
```

### 映射 HTTP 错误时的提示语句
```
/**
 * 映射 HTTP 错误时的提示语句
 * @param {enhanceError} error
 * @returns {Object}
 */
const badStatusMap = error => ({
  400: '请求错误',
  401: '未授权，请登录',
  403: '拒绝访问',
  404: `请求地址出错 ${error.response ? error.response.config.url : ''}`,
  408: '请求超时',
  500: '服务器内部错误',
  501: '服务未实现',
  502: '网关错误',
  503: '服务不可用',
  504: '网关超时',
  505: 'HTTP版本不受支持'
});

```

## 封装axios
---

* http.js
```
/**
 * http.js
 * @param {Object} [globalOption]
 */
import {
  mergeWith, omit, isPlainObject
} from 'lodash';

// isPlainObject ： checks if value is a plain object, that is, an object created by the Object constructor or one with a [[Prototype]] of null
// mergeWith 是用于递归合并来源对象的自身和继承的可枚举属性到目标对象。 
// 基本意思就是将默认的option配置和手动配置的globalOption，通过递归的形式合并成一个option.


/**
 * 修复 axios 转换 bug，如可能把请求中的日期等转成空对象的问题
 *  'utils.merge' in 'node_modules\axios\lib\core\Axios.js'
 * @param {any} obj
 */
const fixAxiosDateMerge = (obj) => {
  if (!obj) return obj;
  if (obj instanceof Date) return obj.getTime();
  if (!isPlainObject(obj)) return obj;
  Object.keys(obj).forEach((key) => {
    obj[key] = fixAxiosDateMerge(obj[key]);
  });
  return obj;
};

function axiosWrapper(globalOption) {
  return function(option) {
    // 此处的操作相对与开放一点，把baseURL、timeout、responseType、可以根据接口不同做单独配置。
    option = mergeWith({
      baseURL: BASE_URL,
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Cache-Control': 'no-cache'
      },
      timeout: TIMEOUT,
      withCredentials: true,
      responseType: '',
      validateStatus: status => !isBadRequest(status)

      // 可以定义HTTP响应状态码的范围。如果`validateStatus`返回`true`（或被设置为`null` promise将被解析;否则，promise将被拒绝。
    }, globalOption, option);

  
    if (option.data) {
      option.data = fixAxiosDateMerge(option.data);
    }
    if (option.params) {
      option.params = fixAxiosDateMerge(option.params);
    }

    const r = axios.create(option);
    
    // 在axios的源代码中，超时后, 会在拦截器那里 axios.interceptors.response 捕抓到错误信息, 且 error.code = "ECONNABORTED"

    // timeout
    r.interceptors.request.use(
      config => config,
      (error) => {
        
        if (error && error.code === 'ECONNABORTED'
          && ~error.message.indexOf('timeout')) {
          console.log('[AxiosWrapper] request timeout');
        }
        return Promise.reject(error);
      }
    );

    // other errors // 跳转到对应的报错页面
    r.interceptors.request.use(
      config => config,
      (error) => {
        const errorInfo = error.response;
        if (errorInfo) {
          const errorStatus = errorInfo.status;
          router.push({
            path: `/error/${errorStatus}`
          });
        }
        return Promise.reject(error);
      }
    );

    // make data
    r.interceptors.request.use(
      (opt) => {
        const params = mergeWith({}, opt.data, opt.params); // cloneDeep(opt.data);
        opt = omit(opt, ['data', 'params']);
        const needBody = /^(put|post|patch)$/i.test(opt.method);
        const sendJSON = opt.headers
          && opt.headers['Content-Type'] === 'application/json';
          // 根据请求头和method来处理入参的格式
        if (needBody) {
          opt.data = sendJSON
            ? JSON.stringify(params)
            : qs.stringify(params);
        } else {
          opt.params = params;
        }
        return opt;
      }
    );

    // bad HTTP request
    r.interceptors.response.use(
      response => response,
      (error) => {
        if (error && error.response) {
          const { status, statusText } = error.response;
          if (isBadRequest(status)) {
            console.warn('[AxiosWrapper] bad HTTP request: status is %s \n', status, error.response);
            error.message = badStatusMap(error)[status] || statusText;
            if (!error.message) {
              try {
                error.message = JSON.parse(error.response.request.responseText).errmsg;
              } catch (ex) {
                console.log('[AxiosWrapper] json parse error', ex);
              }
            }
            Vue.prototype.Config.ROOT_VUE.$message.error(error.message);
            return Promise.reject(new Error(error.message));
          }
        }
        return Promise.reject(error);
      }
    );

    // check business logic
    // 请求失败，根据HTTP响应状态码做出对应的messageTip
    r.interceptors.response.use(
      (response) => {
        if (option.responseType === 'arraybuffer') {
          return response;
        }
        const [reqURL, resData] = takeapartResponse(response);
        const noWarn = 'no-global-config-warn' in response.config.headers
          && !!response.config.headers['no-global-config-warn'];
        const isValid = isValidCode(resData.errno);
        const method = response.config.method.toUpperCase();
        if (!isValid) {
          // eslint-disable-next-line prefer-promise-reject-errors
          return Promise.reject({
            type: ERR_CODE,
            reqURL,
            resData,
            noWarn,
            method
          });
        }
        const reqStr = `${method} ${reqURL}`;
        // warnByResponse 函数是根据业务针对不同接口返回不同的错误提示。这个函数可以根据业务自行处理。
        warnByResponse(reqStr, resData, true, noWarn);
        return resData;
      }
    );
    

    // wrong business logic
    // r.interceptors.response.use(
    //   response => response,
    //   (error) => {
    //     if (error.type === ERR_CODE) {
    //       const reqStr = `${error.method} ${error.reqURL}`;
    //       warnByResponse(reqStr, error.resData, false, error.noWarn);
    //     }
    //     return Promise.reject(error.resData);
    //   }
    // );

    return r(option);
  };
}

```


## 简单的封装axios函数
---

```
/**
 * 获得一般的 wrapper
 * @param {Object} [option]
 */
 // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
export default option => axiosWrapper({
  transformResponse: data => void (0), // eslint-disable-line no-unused-vars
  
})(option);

```
在使用axios时，注意到配置选项中包含params和data两者，以为他们是相同的，实则不然。

因为params是添加到url的请求字符串中的，用于get请求。

而data是添加到请求体（body）中的， 用于post请求。

### api.js使用实例： 
```
import axios, { download } from './http.js';
export const mock = params => axios({
    url: '/mock',
    method: 'get',
    params
});

export const upload = data => axios({
    url: '/upload',
    method: 'post',
    data
});
```
### 用于下载的axiosWrapper

```
export const download = option => axiosWrapper({
  responseType: 'arraybuffer'
})(option)
  .then((res) => {
    const contentType = res.headers['content-type'];

    if (~contentType.indexOf('json')) {
      const resData = res.data || res.request.response;
      const text = Buffer.from(resData).toString('utf8');
      const json = JSON.parse(text);
      return Promise.reject(json);
    }

    const disposition = res.headers['content-disposition'];
    if (disposition && disposition.match(/attachment/)) {
      let filename = disposition.replace(/attachment;.*filename=/, '').replace(/"/g, '');
      filename = filename && filename !== ''
        ? filename
        : (option.defaultFilename || 'noname');
      const blob = new Blob([res.data], { type: contentType });
      saveAs(blob, filename);
    }

    return Promise.resolve(res);
  });
```


api.js使用实例：

```
import axios, { download } from './http';

export const export = (data, defaultFilename) = download({
  url: '/api/export',
  method: 'post',
  data,
  defaultFilename
})

```


## 封装一个axios的思路
---


* 配置请求基本信息的常量：（用于基本处理）
 BASE_URL ：根据环境变量，针对生成、测试、开发环境配置对应的url
 TIMEOUT: 配置接口请求超时时间。
 headers： 配置请求头
 validateStatus: 定义HTTP响应状态码的范围。如果`validateStatus`返回`true`（或被设置为`null` promise将被解析;否则，promise将被拒绝。

可以请求接口的不同，封装axios的时候，将 请求基本信息的常量 通过 参数 的方式单独进行配置。

>扩展：
  具体方法：通过lodash的mergeWith方法将默认的option配置和手动配置的globalOption，通过递归的形式合并成一个option。

* 请求拦截

 timeout: 在axios的源代码中，超时后, 会在拦截器那里 axios.interceptors.response 捕抓到错误信息, 且 error.code = "ECONNABORTED"，可以根据这个code提示超时报错信息。

 token：统一在http请求的header都加上token，这样后台根据token判断你的登录情况，即使本地存在token，也有可能token是过期的，所以在响应拦截器中要对返回状态进行判断

 other errors: 根据请求状态`status`,直接promise.reject.路由跳转到对应的报错页面。
 根据请求头和method来处理入参的格式

在使用axios时，注意到配置选项中包含params和data两者，以为他们是相同的，实则不然。
因为params是添加到url的请求字符串中的，用于get请求。
而data是添加到请求体（body）中的， 用于post请求。

* 响应拦截
 badStatusMap: 请求失败，根据HTTP响应状态码做出对应的messageTip

 ---
总结：大功告成✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️✌️
