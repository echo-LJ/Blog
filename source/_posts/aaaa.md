
## 云图梭Web端：yts-v0.7.0版本性能优化

## 前言：

风和日丽: 我正笑嘻嘻地抓着我炫酷的键盘疯狂的敲着Bug.
晴天霹雳: 突然,大家说我们的网站很卡,需要做性能优化.
难以置信: 手动狗头.
十分抗拒: 迫于yin威,我给网站做了体检和手术.

**`第一版集成打包资源占用情况：`**

![image.png](https://upload-images.jianshu.io/upload_images/11846892-abc3add5daadcf9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**无痕模式下，浏览器访问pre环境，首屏渲染❗️竟然达到了5s\6s的心跳停止一般的渲染效果。**

因pre环境已经部署经过性能优化之后的代码，现以本地服务访问sit数据为例，做个参考(因受网络情况限制，仅供参考)：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-6223e6e6da17575d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 开启起死回生之术💔-只谈结果、省略代码配置过程

1. 删除无用的svg文件
2. 配置打包移除`console`
3. 将第三方静态资源以cdn的方式引入-融云、搜索组件
4. 检验并删除冗余第三方依赖`maia-echarts`等

**经过上述优化，可以发现资源已经被大大压缩❗️两个首屏加载资源已经被压缩到原来的1/5，仿佛又有了心跳的迹象！**


![image.png](https://upload-images.jianshu.io/upload_images/11846892-e2186e237a6c30d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](https://upload-images.jianshu.io/upload_images/11846892-f67cb00e7d712cfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因pre环境已经部署性能优化之后的代码，现以本地服务访问sit数据为例，做个参考(因受网络情况限制，仅供参考)：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-6f382e8fbc8b68d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 继续❤️心脏复苏❤️

1. 删除第三方依赖`moment`插件默认引入本地包，如需特殊语言，开发手动配置。

![image.png](https://upload-images.jianshu.io/upload_images/11846892-6f97038d280f3eba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-c7586ec91c648d9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2. 删除第三方依赖引入的同名依赖


![image.png](https://upload-images.jianshu.io/upload_images/11846892-412924f5fbf41389.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-8eafaea14b88730d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 删除冗余第三方依赖`vue-draggable-resizable`
4. 删除冗余第三方依赖`vue-i18n`

![image.png](https://upload-images.jianshu.io/upload_images/11846892-292d2297a988c456.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-95874e77a292b2f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 拔掉氧气罐，我们来看看！

经过一系列抢救，它睁眼了❗️

让我们来看看pre环境，无痕模式下首次加载情况。已经达到了毫秒级（因受网络情况限制，仅供参考）：
![image.png](https://upload-images.jianshu.io/upload_images/11846892-9f20b6742f872570.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 后记

本次‘救治’情况，因受`上线时间`、`其他需求`等不可抗拒因素，暂时停止，后期开发过程中持续维护。

## 通报批评（不具有针对性，如有冒犯，请查找自身原因）

为督促大家友好供养一个产品，现针对本次救治过程中部分'家长'存在`投毒`、`屡教不改`情况作出批评！


❗️经过多次提醒：

![image.png](https://upload-images.jianshu.io/upload_images/11846892-8c6aaf8593828ab3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-7dc0bda5f1461b12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/11846892-75a09f03f7496632.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


❗️但是！还是没有删除干净，被框架组检查出来了！

![image.png](https://upload-images.jianshu.io/upload_images/11846892-dca101dd571c278d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**`做一个产品，就像养一个孩子一样，需要各位`家长`投入耐心，精选投食产品，现推出以下几种可以帮助`孩子`健康成长的必须食品！`**


## 云图梭Web端:性能优化、SEO优化要求--必须

##### 1. 删除无用的svg文件
##### 2. 组件按需引入------后期需要优化
*  项目中现存的引入组件的方式，改成按需引入❗️❗️❗️❗️❗️ 
##### 3.  将第三方静态资源以cdn的方式引入`----后期需要优化`
*  项目中存在的融云相关的配置文件，以cdn方式引入❗️❗️❗️❗️❗️ 
##### 4.移除`console`

* console 可能造成内存泄露

⚠️： 现项目中已经配置打包自动移除`console.log`

##### 5.优化未设置尺寸的图片元素`----后期需要优化`
* 图片元素设置显式的宽度和高度,从而减少布局偏移
* img标签设置alt: 有利于SEO

举个🌰

```
<img src="hello.png" width="50" height="50" alt="Hello World" />
```
*  项目中部分img标签缺少seo❗️❗️❗️❗️❗️ 
##### 6.避免 document.write()`----后期需要优化`

* 对于连接速度较慢的用户，通过 `document.write()` 动态注入的外部脚本可以将页面加载延迟数十秒
*  项目中以 `document.write()`引入待办不同环境的sdk，后期优化成 根据 区分环境打包 根据环境区分引入不同的sdk(需要配置devops)❗️❗️❗️❗️❗️ 
##### 7.避免过大的dom元素`----后期需要优化`
*  项目中`approval_card_im.svg`资源过大❗️❗️❗️❗️❗️ 
##### 8.避免使用gif `----后期需要优化`
*  项目中待办模块中使用的loading.gif考虑是否改成video形式。目前占用字节过大。❗️❗️❗️❗️❗️ 
##### 9.使用http2 `----后期需要优化`
*  项目中接口请求是http2， 资源请求基本都是http1❗️❗️❗️❗️❗️ 
##### 10.缺少meta标签 `----后期需要优化`
```
<meta name="description" content="Put your description here.">
```
*  项目中缺少meta-description： 如需优化，需产品提供文案。❗️❗️❗️❗️❗️ 

##### 11.css提取公共样式 `----后期需要优化`
*  项目中请使用混合scss,公共color、bgColor、padding等。❗️❗️❗️❗️❗️ 






